---
layout: post
title: "Import Cycles in Golang: How To Deal With Them"
description: Post explaining how the import cycle occurs and how you can deal with them
comments: true
keywords: go, golang, import cycle
scover:
---

As a Golang developer, you probably have encountered import cycles. Golang do not allow import cycles. Go throws a compile-time error if it detects the import cycle in code. In this post, let's understand how the import cycle occurs and how you can deal with them.

### Import Cycles

Let’s say we have two packages, `p1` and `p2`. When package `p1` depends on package `p2` and package `p2` depends on package `p1`, it creates a cycle of dependency.  Or it can be more complicated than this eg. package `p2` does not directly depend on package `p1` but `p2` depends on package `p3` which depends on `p1`, again it is cycle.

<img class="fullimg" alt="import cycle golang" src="https://user-images.githubusercontent.com/20956124/103145320-6c099f80-475e-11eb-8d24-9112b5e23dee.png">

Let’s understand it through some example code.

### Import Cycles Are Bad Design

Go is highly focused on faster compile time rather than speed of execution (even willing to sacrifice some run-time performance for faster build). The Go compiler doesn’t spend a lot of time trying to generate the most efficient possible machine code, it cares more about compiling lots of code quickly.

Allowing cyclic/circular dependencies would **significantly increase compile times** since the entire circle of dependencies would need to get recompiled every time one of the dependencies changed. It also increases the link-time cost and makes it hard to test/reuse things independently (more difficult to unit test because they cannot be tested in isolation from one another). Cyclic dependencies can result in infinite recursions sometimes.

Cyclic dependencies may also cause memory leaks since each object holds on to the other, their reference counts will never reach zero and hence will never become candidates for collection and cleanup.

### :hammer_and_wrench: Debugging Import Cycles

The worst thing about the import cycle error is, Golang doesn’t tell you source file or part of the code which is causing the error. So it becomes tough to figure out when the codebase is large. You would be wondering around different files/packages to check where actually the issue is. Why golang do not show the cause that causing the error? Because there is not only a single culprit source file in the cycle.

But it does show the packages which are causing the issue. So you can look into those packages and fix the problem.

### Dealing with Import Cycles

When you encounter the import cycle error, take a step back, and think about the project organization. The obvious and most common way to deal with the import cycle is implementation through interfaces. But sometimes you don’t need it. Sometimes you may have accidentally split your package into several. Check if packages that are creating import cycle are tightly coupled and they need each other to work, they probably should be merged into one package. In Golang, a package is a compilation unit. If two files must always be compiled together, they must be in the same package.

#### The Interface Way:

- Package `p2` to use functions/variables from package `p1` by importing package `p1`.
- Package `p1` to call functions/variables from package `p2` without having to import package `p2`. All it needs to be passed package `p2` instances which implement an interface defined in `p1`, those instances will be views as package `p1` object.

That’s how package `p1` ignores the existence of package `p2`.

### Bottom Lines

The import cycle is definitely a pain when the codebase is large. Try to build the application in layers. The higher-level layer should import lower layers but lower layers should not import higher layer (it create cycle). Keeping this in mind and sometimes merging tightly coupled packages into one is a good solution than solving through interfaces. But for more generic cases, interface implementation is a good way to break the import cycles.

I hope you got a fair understanding of Import Cycles. Do share and reach out to me on [Twitter](https://twitter.com/jogendrafx) in case of anything. Thanks for read :)
