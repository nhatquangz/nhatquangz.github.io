---
title: Change iOS status bar style in different ways
date: 2020-07-10
---

In iOS, there are many ways to change status bar style (light or dark). In this article, we'll go through these ways.

## Change globally

By setting up some fields in **Info.plist** file, all of your viewcontrollers will have the same status bar style. If you do not have plan to change your app's status bar, you can consider this method.

1. Open you **Info.plist**.
2. Add **View controller-based status bar appearance** key (`UIViewControllerBasedStatusBarAppearance`) and set value to **No** (`false`).
3. Add **Status bar style** key (`UIStatusBarStyle`) and set value to **Light Content** (`UIStatusBarStyleLightContent`).

![Status Setup][status-bar-infoplist]{: .align-center}

In this method, if you want to change the style, there only one way to do it:

```swift
//Set Style
UIApplication.shared.statusBarStyle = .default
```

But [statusBarStyle](https://developer.apple.com/documentation/uikit/uiapplication/1622988-statusbarstyle) were deprecated from iOS 9. So, if your app has a different status bar style for some view controller, you might have to consider other methods. For a simple app, this might be what you are looking for.

## Style based on view controllers

To setup status bar style depended on viewcontrollers, you need to make sure the value of **View controller-based status bar appearance** key in **Info.plist** is set to NO, or simply, just delete the field. The default behavior is already using a view controller-based status bar.

For each viewcontroller that you want to change the status bar style, you just need to override **[preferredStatusBarStyle](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621416-preferredstatusbarstyle)** property to return the style you want.

```swift
override var preferredStatusBarStyle: UIStatusBarStyle {
    return .lightContent
}
```

If you want to change status bar style dynamically,  you can tell the viewcontroller to update the style by calling **[setNeedsStatusBarAppearanceUpdate()](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621354-setneedsstatusbarappearanceupdat)** method.

```swift
var statusBarStyle: UIStatusBarStyle = .lightContent

override var preferredStatusBarStyle: UIStatusBarStyle {
    return statusBarStyle
}

func changeStatusBarStyle() {
	statusBarStyle = .darkContent
	setNeedsStatusBarAppearanceUpdate()
}
```

If you call this method within an animation block, the changes are animated along with the rest of the animation block.

```swift
func changeStatusBarStyle() {
	statusBarStyle = .darkContent
	UIView.animate(withDuration: 0.3) {
    	self.setNeedsStatusBarAppearanceUpdate()
    }
}
```

![status-bar-animation][status-bar-animation]{: .align-center}

### Navigation Controllers

In case your view controller were embeded in a NavigationController, the navigation controller now takes over control of the style of the status bar, and your view controller’s **preferredStatusBarStyle** property will be ignored.

If you hide navigation bar, your viewcontroller will be in charge of status bar style again.
{: .notice--warning}

If you want to adjust the style of the status bar in a navigation controller, you’ll need to do this in the navigation bar’s style property:

```swift
override func viewDidAppear(_ animated: Bool) {  
	navigationController?.navigationBar.barStyle = .black 
}
```

Or you can create a custom navigation controller and set **preferredStatusBarStyle** normally:

```swift
class StatusBarStyleNavigationController: UINavigationController {
	override var preferredStatusBarStyle: UIStatusBarStyle {
		return .lightContent
	}
}
```

## In summary

**If View controller-based status bar appearance = NO:**
+ Using Info.plist file to setup status bar style

**If View controller-based status bar appearance = YES:**
+ Override **preferredStatusBarStyle** in your view controller classes
+ Set style of navigation controllers / navigation bars.

[status-bar-infoplist]: /assets/images/posts/status-bar-infoplist.png
[status-bar-animation]: /assets/images/posts/status-bar-animation.gif