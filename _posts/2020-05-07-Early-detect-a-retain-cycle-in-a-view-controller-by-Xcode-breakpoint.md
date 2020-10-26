---
title: Early detect a retain cycle in a view controller by Xcode breakpoint
toc: false
---
## Memory Leak

A memory leak occurs when a given memory space can't be recovered by the ARC ([Automatic Reference Count](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)) because it is unable to know whether this memory space is actually in use or not. A common result of leaking memory in iOS is dismissed viewcontrollers are still not be released. And many iOS developers come up with this in viewcontroller:

```swift
deinit {
    print("deinit \(self)")
}
```

`deinit` function will not be called if there is a leak in the viewcontroller. It works but it's definitely not elegant. We don't want to have this boilerplate code in every viewcontroller. 

## Detecting memory leaks

Apple offers some powerfull tools for debugging memory leaks like **Instrument** or **Memory Graph Debugger.** But when you should use these sophisticated tools?

There is a very nice tip from [Cédric Luthi](https://twitter.com/0xced/status/900692839557992449) that I want to share with you today. It can tell you when you need to stop to check carefully your code and debug memory leaks. He uses **Symbolic Breakpoint** to detect view controllers not being deallocated. The same concept as the print function in `deinit`, but a lot cleaner. You don't populate your code with print this way.

1. Go to **Breakpoint Navigator**
    
    ![Breakpoint Navigator][breakpoint-navigator]

2. Click **+** button and choose **Symbolic Breakpoint**

    ![Symbolic Breakpoint][symbolic-breakpoint]

3. Set fields with value:

    **Symbol**: `-[UIViewController dealloc]`
    
    **Module**: `UIKitCore`

    **Condition**: `!(BOOL)([[$arg1 description] containsString:@"Input"])` to prevent printing some system viewcontrollers like UIInputViewController, UICompatibilityInputViewController…

4. Set an action to **Log Message** and set a message to whatever you want to print to the console when a view controller gets deallocated. To print valuable information, you can set it with value:

    `-- dealloc @(id)[$arg1 description]@ @(id)[$arg1 title]@`

5. Finally, check **Automatically continue after evaluating actions** option as you don't want a debugger to pause when a view controller gets deallocated.

![Breakpoint Setup][breakpoint-setup]{: .align-center}

So now with this breakpoint in place, whenever you dismiss a viewcontroller or pop it out of a navigation stack, you will see a log with the name of released viewcontroller in debug console. That's mean everything is working as expected. If not, you have memory leaks.

## User breakpoint level

Regular breakpoints are stored inside the personal settings for particular workspace or project and are visible only for you, even if you commit your personal settings to the project. Colleagues working on the same project will not see your breakpoints in their Xcode.

This breakpoint is very helpful and you will want to have it in every projects. To do it without repeating above setup, you can move the breakpoint to user level. This moves the breakpoint out of the workspace or project scope and into a user-wide scope. This means the breakpoint then shows up on your machine in all projects.

![User Breakpoint][user-breakpoint]



[breakpoint-navigator]: /assets/images/posts/Breakpoint-Navigator.png
[symbolic-breakpoint]: /assets/images/posts/symbolic-breakpoint.png
[breakpoint-setup]: /assets/images/posts/breakpoint-setup.png
[user-breakpoint]: /assets/images/posts/user-breakpoint.png