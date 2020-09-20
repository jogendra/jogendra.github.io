---
layout: post
title: Implementing Launch at Login Feature in MacOS Apps
description: How to launch a completely sandboxed macOS application on system startup
comments: true
keywords: swift, macos, swiftui, open-source, apple, xcode
scover: launch_at_login.png
---
In this post, I will explain, how you can add the Launch at Login (launch application on system startup) feature to your MacOS Applications. Adding this feature is way more complex than you would expect. I use [MeetingBar](https://github.com/leits/MeetingBar) for managing my work/personal meetings over Google Meet and Zoom. MeetingBar is an open-source MacOS App. Recently, I added the Launch at Login feature to MeetingBar. Initially, I thought that there will be a simple solution to it and it will take minutes only. But when I started implementing it, I found out that it is not really that straight forward. You can find the implementation source code below.

Link to Pull Request: [Add Launch at Login feature](https://github.com/leits/MeetingBar/pull/76)

Source Code for this post: [https://github.com/jogendra/launch-at-login-macos](https://github.com/jogendra/launch-at-login-macos)

For the users, a straightforward solution is going to system preference and enable auto launching for applications (_Preferences -> Users & Groups -> Login Items_). But it is always good if the application itself offers the feature from its preference. For application developers, it's a bit tricky.

There are a couple of ways to add a login item to the application. The most used/useful way is using the [Service Management framework](https://developer.apple.com/documentation/servicemanagement). Login items installed using the Service Management framework are not visible in System Preferences and can only be removed by the application that installed them.
The other way is using a shared file list. Login items installed using a shared file list are visible in System Preferences; users have direct control over them. If you use this API, your login item can be disabled by the user.

In this post, I will explain how to add login items to MacOS Applications using the Service Management framework. The idea is to create an application inside the main application that handles the auto launching for the main application.

## Little bit about Apple App Sandboxing

According to Apple documentation, to distribute a macOS app through the Mac App Store, you must enable the App Sandbox capability. App Sandbox provides protection to system resources and user data by limiting your app’s access to resources requested through entitlements. Apps signed and distributed outside of the Mac App Store with Developer ID can (and in most cases should) use App Sandbox as well. App Sandbox enables you to describe how your app interacts with the system. The system then grants your app the access it needs to get its job done and no more. It is always best to design your apps with App Sandbox in mind. You can check out this detailed [article by Appcoda on Mac App Sandbox](https://www.appcoda.com/mac-app-sandbox/).

Every application is given a sandbox, a directory it can use to store data. An application cannot access the sandbox of another application. If the application needs access to data on the device that isn't located in the application's sandbox, it needs to request the data through a system interface. System interfaces add a layer of security and protect the user's privacy.

<img class="fullimg" alt="apple app sandboxing" src="https://user-images.githubusercontent.com/20956124/93572750-ea161780-f9b3-11ea-8f32-e94a6b5a2672.png">

To adopt App Sandbox for a target in an Xcode project, apply the `<true/>` value to the `com.apple.security.app-sandbox` entitlement key for that target. Do this in the Xcode target editor by selecting the Enable App Sandboxing checkbox.

<!-- ```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
</dict>
</plist>
``` -->

<img class="fullimg" alt="entitlement" src="https://user-images.githubusercontent.com/20956124/93665547-f45c1280-fa94-11ea-96d4-7dbdabd83172.png">

Generally, when you create macOS App, it is already added, you don’t have to worry about it.

## :zap: Initial Setups

In this post, we will use **MainApplication** for the main application (using SwiftUI) in which we are going to add Launch at Login functionality and the other one will be **AutoLauncher**, which is our _helper application_ that is responsible for registering our main application for login items.

<img class="fullimg" alt="main application general settings" src="https://user-images.githubusercontent.com/20956124/93586575-218dbf80-f9c6-11ea-8a93-af7699914094.png">

## :hammer_and_wrench: Adding Helper Target

To add helper target AutoLauncher to our MainApplication, go to _File -> New -> Target_ and choose **macOS** template with **App** selected (under _Application_ section) and click `Next` button to choose options for helper application.

<img class="diffimg" alt="select target templete" src="https://user-images.githubusercontent.com/20956124/93664146-62e7a300-fa8a-11ea-9199-086f3674c806.png">

In next screen, choose _'AutoLauncher'_ for `Product Name` (or whatever name you want to give), select _'Storyboard'_ option for the `User Interface` option (we will delete this interface later anyway), make sure the `Project` option has _'MainApplication'_ target selected and finish the setup.


<!-- <img class="fullimg" alt="select project options" src="https://user-images.githubusercontent.com/20956124/93589295-3d936000-f9ca-11ea-804e-31cc262c8b0c.png"> -->

## :gear: Helper Target Configurations

- Since our helper target AutoLauncher should be background only as we don't need user interface for AutoLauncher. So to make it background only service, go to `Info.plist` of AutoLauncher, set `Application is background only` key to `YES`.

<!-- <img class="fullimg" alt="AutoLauncher Info.plist" src="https://user-images.githubusercontent.com/20956124/93661127-f3b28480-fa72-11ea-8e74-b734d6ccd814.png"> -->
<img class="fullimg" alt="AutoLauncher Info.plist" src="https://user-images.githubusercontent.com/20956124/93661035-36279180-fa72-11ea-8350-5e58078d83a9.png">

- Go to _Targets_, select _'AutoLauncher'_ Target's Build Settings, and search for _"skip install"_. Set `Skip Install` to `Yes`. Otherwise we would end up with too many bundles in our final app archive.

<img class="fullimg" alt="Skip Install Settings" src="https://user-images.githubusercontent.com/20956124/93661621-be0f9a80-fa76-11ea-9319-7953df61180c.png">

- Go to `AppDelegate.swift` of AutoLauncher, **rename** class name `AppDelegate` to `AutoLauncherAppDelegate` (or whatever name you want to give) because it is conflicting with `AppDelegate` class of our MainApplication. We will configure this Delegate class method `applicationDidFinishLaunching(_ :)` like below. It check if MainApplication is running, if not, launch application using [`openApplication(at:_:_:)`](https://developer.apple.com/documentation/appkit/nsworkspace/3172700-openapplication). This instance method launches the app at the specified path URL.

<!-- ```swift
import Cocoa

@NSApplicationMain
class AutoLauncherAppDelegate: NSObject, NSApplicationDelegate {
    
    struct Constants {
        // Bundle Identifier of MainApplication target
        static let mainAppBundleID = "com.jogendra.MainApplication"
    }
    
    func applicationDidFinishLaunching(_ aNotification: Notification) {
        let runningApps = NSWorkspace.shared.runningApplications
        let isRunning = runningApps.contains {
            $0.bundleIdentifier == Constants.mainAppBundleID
        }
        
        if !isRunning {
            var path = Bundle.main.bundlePath as NSString
            for _ in 1...4 {
                path = path.deletingLastPathComponent as NSString
            }
            let applicationPathString = path as String
            guard let pathURL = URL(string: applicationPathString) else { return }
            NSWorkspace.shared.openApplication(at: pathURL,
                                               configuration: NSWorkspace.OpenConfiguration(),
                                               completionHandler: nil)
        }
    }
    
}
``` -->

<img class="fullimg" alt="App delegate code" src="https://user-images.githubusercontent.com/20956124/93665403-cfb36b00-fa93-11ea-9655-01a2a441e553.png">

This AutoLauncher app target is actually embedded inside the MainApplication bundle under the subdirectory `Contents/Library/LoginItems` (we are going to do this later). So including the helper app target name (AutoLauncher) there will be a total of **4 path components** to be deleted. That's why we are running loop 4 times.

## :anger: Cleaning Up Helper Target UI Elements

Helper target app (AutoLauncher) should not have any UI elements. We will remove all the visual elements from the app, even the _Main.storyboard_. Now, got ahead and:
- Delete `ViewController.swift` file.
- Delete `Assets.xcassets` folder.
- Delete `Main.storyboard`.

Since we have deleted entry point of AutoLauncher target which was earlier configured in deleted `Main.storyboard` file. To make that up, follow:
- Create and add `main.swift` file (please note it has to be _”main”_ with lowercased  _”m”_, otherwise it won’t work) to AutoLauncher.
- Remove `@NSApplicationMain` from `AutoLauncherAppDelegate` class. `@NSApplicationMain` is no longer needed because in-general `@NSApplicationMain` implicit generate the `main.swift` file for us which we have already created.
- Set the Application delegate like below in `main.swift`:

<img class="fullimg" alt="main.swift code" src="https://user-images.githubusercontent.com/20956124/93665199-40f21e80-fa92-11ea-97d2-a890926ce06c.png">

Clean? We are done with all the setups for helper. Now, let's configure our Main Application.

## :electric_plug: Adding Helper to Main Application

We have to copy the helper application AutoLauncher inside our MainApplication. To do so, go to Project Setting, have _`MainApplication`_ target selected, select _Build Phases_, click **`+`** icon shown below, and select `New Copy Files Phase`.

<img class="fullimg" alt="New Copy Files Phase" src="https://user-images.githubusercontent.com/20956124/93669587-e1583b00-fab2-11ea-9c78-a947854cd756.png">

Apply Settings as below:

Select **Wrapper** as `Destination`, put **_Contents/Library/LoginItems_** in `Subpath` and finally click on **+** icon to add `AutoLauncher.app` there (it will be inside _Product_ folder).

<img class="fullimg" alt="New Copy Files Phase" src="https://user-images.githubusercontent.com/20956124/93670076-07330f00-fab6-11ea-9160-2b257b0a0411.png">

Now, go to `General` tab and Add **ServiceManagement** framework to `Frameworks, Libraries, and Embedded Content` section.

You can now import `ServiceManagement` to any of your swift file and use the [**`SMLoginItemSetEnabled(_:_:)`**](https://developer.apple.com/documentation/servicemanagement/1501557-smloginitemsetenabled?language=swift) function to enable a helper application. It takes two arguments, a `CFStringRef` containing the bundle identifier of the helper application, and a `Boolean` specifying the desired state. This value is effective only for the currently logged-in user. Pass **`true`** to start the helper application immediately and indicate that it should be started every time the user logs in. Pass **`false`** to terminate the helper application and indicate that it should no longer be launched when the user logs in. This function returns true if the requested change has taken effect; otherwise, it returns false. This function can be used to manage any number of helper applications.

#### Example UI

<img class="diffimg" alt="Example UI for Launch at Login" src="https://user-images.githubusercontent.com/20956124/93679403-02c52100-facb-11ea-898b-36989d11d03e.png" style="width:200px !important">

In this example, I just added a simple toggle using SwiftUI. On toggle, it's calling `SMLoginItemSetEnabled` function with helper app identifier and current state of toggle. Further, you can store current state in UserDefaults.

<img class="fullimg" alt="Example UI for Launch at Login" src="https://user-images.githubusercontent.com/20956124/93672414-4b2f0f80-fac8-11ea-968e-5f80bf6f1241.png">

**NOTE**: If multiple applications (for example, several applications from the same organization) contain a helper application with the same bundle identifier, only the one with the greatest bundle version number is launched. Any of the applications that contain a copy of the helper application can enable and disable it. You should keep this in mind if you are shipping applications under the same organization. You can read more in detail about this [here](https://www.appcoda.com/app-group-macos-ios-communication/).

### :link: Helpful Links

1. [App Sandbox in Depth](https://developer.apple.com/library/archive/documentation/Security/Conceptual/AppSandboxDesignGuide/AppSandboxInDepth/AppSandboxInDepth.html#//apple_ref/doc/uid/TP40011183-CH3-SW4)
2. [Creating a macOS App in SwiftUI](https://developer.apple.com/tutorials/swiftui/creating-a-macos-app)
3. [SMLoginItemSetEnabled Method Documentations](https://developer.apple.com/documentation/servicemanagement/1501557-smloginitemsetenabled?language=swift)
4. [Beyond App Sandbox: Going outside of the macOS app container](https://www.appcoda.com/mac-app-sandbox/)
5. [Using App Groups for communication between macOS/iOS apps from the Same Vendor](https://www.appcoda.com/app-group-macos-ios-communication/)

### :memo: Closing Notes

We have seen how we can add login items to any MacOS application. You can [**Download Full Source Code Here**](https://github.com/jogendra/launch-at-login-macos). Adding login items to macOS applications isn't that straight-forward. You need to follow all the setps carefully. I hope this post helped you in some way. Do share and [drop me suggestions/questions on Twitter](https://twitter.com/jogendrafx). Thanks for read :)
