---
title: Some handy Xcode shotcuts to improve your productivity
excerpt: As developers, we spend most of our time in our IDE, so it’s worth becoming an expert in using it.
topimage: https://source.unsplash.com/TZj-urJKRao/900x450
---

As developers, we spend most of our time in our IDE, so it’s worth becoming an expert in using it. Taking the time to memorize and practice these shortcuts will save you a lot of time having to reach for your mouse and will allow you to keep both your hands on the keyboard, more often.

We’ll walk through some Xcode keyboard shortcuts that I found useful. I hope they help you improve your efficiency as a developer too.

- ⌘ = Command
- ⌥ = Option/Alt
- ⇧ = Shift
- ⌃ = Control
- ←→ ↑↓ = Arrow keys
- ↩ = Enter

## Editing

In Xcode, we spend most of our time using the editor, writing code. Therefore, knowing a few common keyboard shortcuts while writing and editing the code just makes sense.

### Code Completion (**⌃ + Space)**

I always appreciate IDEs with a strong code completion feature. It makes us work more faster and feel comfortable as we don't have to remember exactly a lot of long function's names. You know Apple always prefers not to abbreviate function's names to make their framework more clean and easily  understandable. It’s hard to imagine working without code completion.

![Xcode Code Completion][code-completion]

### Moving Lines

move up: **⌥ + ⌘ + [** <br>
move down: **⌥ + ⌘ + ]** <br>
When you want to move single lines or entire blocks around in your code, you can use **`⌘ + X`** to cut and **`⌘ + V`** to paste it to where you want if the new position is far from the original one. However, if you just want to move your code few line up/down, using moving lines shortcuts can be immensely useful. Xcode automatically takes care of indentation.

![Xcode Move Line][xcode-move-line]

### Create description of function (**⌘ + ⌥ + /)**

We can use **`⌘ + /`** to comment a bunch of lines in your code. But you may not know that you can use **`⌘ + ⌥ + /`** to create function's description, very useful to document your code.

![XCode function description][function-description]

### Balance Indentation (**⌃ + I)**

Another useful keyboard shortcut to help you keep your code clean and neatly formatted. You can select a block to format or quickly format all your code by **`⌘ + A`** then **`⌃ + I`.**

![Xcode Format Code][xcode-format-code]

## Navigation

Knowing how to navigate inside Xcode effectively will help you easily switch contexts. It can also allow you to find what you are searching for without having to check for it manually.

### Open Quickly (**⇧ + ⌘ + O)**

Most of IDEs have this functions, I use it most frequently, all the time. **Open quickly** allows you to quickly jump into any source location in your project / workspace. Just start typing the name of any class, interface, function, method, enum, … in your project.

![XCode Open Quickly][open-quickly]

### Going Back and Forth (⌃ + ⌘ + ← and ⌃ + ⌘ + →)

Xcode tracks your entire movement history as soon as you open or create a project. Any file you go to, any symbol you look up - it all is appended to the IDE’s navigation stack. Use `⌃ + ⌘ + ←` and `⌃ + ⌘ + →` to go back and forth between the source code locations you visited. If you use a touch pad, you can swipe back/forth using 2 finger.

![XCode Move Back and Forth][move-back-forth]

### Reveal in Project Navigator (**⇧ + ⌘ + J**)

When your project get larger, you have a lot of folders and files opened and sometime you may be lost in Project Navigator. **`⇧ + ⌘ + J`** can help you immediately focus on current file in project navigator.

![Reveal in Project Navigator][reveal-project-navigator]

### Searching **⇧ + ⌘ + F**

You can use **Open Quickly** feature to search files / functions, but sometime you need to search some text in entire your project. **`⇧ + ⌘ + F`** will bring you to Find navigator.

![XCode Searching][xcode-searching]

## Conclusion

It could at first seems a little daunting to master the keyboard shortcuts. But it's worth to learn and once you've got it, you will find yourself using theme without thinking about it. If you want more other shortcuts, you can open K**ey Bindings** menu and discover yourself.

XCode —> Preferences —> Key Bindings

![Key Bindings][key-bindings]


[code-completion]: /assets/images/posts/code-completion.png
[xcode-move-line]: /assets/images/posts/move-line-xcode.gif
[function-description]: /assets/images/posts/document-function.gif
[xcode-format-code]: /assets/images/posts/xcode-format-code.gif
[open-quickly]: /assets/images/posts/open-quickly.gif
[move-back-forth]: /assets/images/posts/move-back-forth.gif
[move-back-forth]: /assets/images/posts/move-back-forth.gif
[reveal-project-navigator]: /assets/images/posts/reveal-project-navigator.gif
[xcode-searching]: /assets/images/posts/xcode-searching.gif
[key-bindings]: /assets/images/posts/key-binding.png