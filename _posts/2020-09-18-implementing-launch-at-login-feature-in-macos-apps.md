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

For the users, a straightforward solution is going to system preference and enable auto launching for applications (_Preferences -> Users & Groups -> Login Items_). But it is always good if the application itself offers the feature from its preference. For application developers, it's a bit tricky.

There are a couple of ways to add a login item to the application. The most used/useful way is using the [Service Management framework](https://developer.apple.com/documentation/servicemanagement). Login items installed using the Service Management framework are not visible in System Preferences and can only be removed by the application that installed them.
The other way is using a shared file list. Login items installed using a shared file list are visible in System Preferences; users have direct control over them. If you use this API, your login item can be disabled by the user.

In this post, I will explain how to add login items to MacOS Applications using the Service Management framework. The idea is to create an application inside the main application that handles the auto launching for the main application.

## Little bit about Apple App Sandboxing

According to Apple documentation, to distribute a macOS app through the Mac App Store, you must enable the App Sandbox capability. App Sandbox provides protection to system resources and user data by limiting your app’s access to resources requested through entitlements. Apps signed and distributed outside of the Mac App Store with Developer ID can (and in most cases should) use App Sandbox as well. App Sandbox enables you to describe how your app interacts with the system. The system then grants your app the access it needs to get its job done and no more. It is always best to design your apps with App Sandbox in mind. You can check out this detailed [article by Appcoda on Mac App Sandbox](https://www.appcoda.com/mac-app-sandbox/).

Every application is given a sandbox, a directory it can use to store data. An application cannot access the sandbox of another application. If the application needs access to data on the device that isn't located in the application's sandbox, it needs to request the data through a system interface. System interfaces add a layer of security and protect the user's privacy.

<img class="fullimg" alt="apple app sandboxing" src="https://user-images.githubusercontent.com/20956124/93572750-ea161780-f9b3-11ea-8f32-e94a6b5a2672.png">

To adopt App Sandbox for a target in an Xcode project, apply the `<true/>` value to the `com.apple.security.app-sandbox` entitlement key for that target. Do this in the Xcode target editor by selecting the Enable App Sandboxing checkbox.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
</dict>
</plist>
```

Generally, when you create macOS App, it is already added, you don’t have to worry about it.

## Initial Setups

In this post, we will use **MainApplication** for the main application (using SwiftUI) in which we are going to add Launch at Login functionality and the other one will be **AutoLauncher**, which is our _helper application_ that is responsible for registering our main application for login items.

<img class="fullimg" alt="main application general settings" src="https://user-images.githubusercontent.com/20956124/93586575-218dbf80-f9c6-11ea-8a93-af7699914094.png">

## Adding Helper Target

To add helper target AutoLauncher to our MainApplication, go to _File -> New -> Target_ and choose **macOS** template with **App** selected (under _Application_ section) and click `Next` button to choose options for helper application.

<img class="fullimg" alt="select target templete" src="https://user-images.githubusercontent.com/20956124/93589285-3b310600-f9ca-11ea-9fd5-67ceccb40792.png">

In next screen, choose _'AutoLauncher'_ for `Product Name` (or whatever name you want to give), select _'Storyboard'_ option for `User Interface` option (we will delete this interface later anyways), make sure `Project` option have _'MainApplication'_ target selected and finish the setup.


<!-- <img class="fullimg" alt="select project options" src="https://user-images.githubusercontent.com/20956124/93589295-3d936000-f9ca-11ea-804e-31cc262c8b0c.png"> -->

## Helper Target Configurations