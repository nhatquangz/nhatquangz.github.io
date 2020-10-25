---
title: Property Wrapper in Swift 5.1
---
When working with SwiftUI, you may encounter some new attributes such as `@State` , `@StateObject` or `@ObservedObject`... These are **[property wappers](https://docs.swift.org/swift-book/LanguageGuide/Properties.html#ID617)**, a new feature comes with Swift 5.1. 

>A property wrapper adds a layer of separation between code that manages how a property is stored and the code that defines a property. 

Swift 5.1’s *property wrappers* feature can be incredibly useful, as it enables us to to define a custom type, that implements behavior from `get` and `set` methods — which often opens up new opportunities for code reuse and generalization. In this article, let’s take a look at how property wrappers work, and explore a few examples of situations in which they could be used in practice.

## How Property Wrappers work

Let's start with a simple example to have better understand property wrappers and which problems do they resolve. Say, we want to create a interger property that only accepts even values assigned to it. We have something like this:

```swift
struct Foo {
    private var _x: Int = 0
    var evenValue: Int {
        get { _x }
        set { _x = newValue % 2 == 0 ? newValue : _x }
    }
}

var f = Foo()
print(f.evenValue) // 0

f.evenValue = 2
print(f.evenValue) // 2

f.evenValue = 3
print(f.evenValue) // 2
```

It works well, but if we want to have more variables with this constraint, the code will become a mess soon. Instead of duplicating the constraint logic over and over for every new property, we can reuse the logic like this:

```swift
struct EvenValue {
    private var value: Int
    init(wrappedValue: Int) {
        self.value = wrappedValue
    }
    
    var wrappedValue: Int {
        get { value }
        set { value = newValue % 2 == 0 ? newValue : value }
    }
}

struct Foo {
    private var _x = EvenValue(wrappedValue: 0)
    var evenValue: Int {
        get { _x.wrappedValue }
        set { _x.wrappedValue = newValue }
    }
}
```

More better, the logic is reused. But we still have to write repeatedly `get` and `set` for these properties. Fortunately, Swift provides first-class language support for this pattern. All we need to do is add the `@propertyWrapper` attribute to our `EvenValue` type:

```swift
@propertyWrapper
struct EvenValue {
	// The rest of the code is unchanged
}

struct Foo {
		@EvenValue var evenValue: Int = 0
}

var f = Foo()
print(f.evenValue) // 0

f.evenValue = 2
print(f.evenValue) // 2

f.evenValue = 3
print(f.evenValue) // 2
```

The attribute `@EvenValue` is a syntactic sugar, which hides the previous version of our code.

## Create Property Wrappers

We can use `class` or `struct` which is defined with the attribute **@propertyWrapper** to create a property wrapper type. And it must have a **wrappedValue** property.

```swift
@propertyWrapper
struct Wrapper<T> {
   var wrappedValue: T
}
```

We can now use the attribute @Wrapper at the property declaration site:

```swift
struct Foo {
    @Wrapper var x: Int
}

let a = Foo(x: 0)
```

We can pass a default value to the wrapper in two ways:

```swift
struct Foobar {
    @Wrapper var x = 0 // 1
    @Wrapper(wrappedValue: 0) var y // 2
}
```

1. The compiler implicitly uses `init(wrappedValue:)` to initialize `x` with `0`.
2. The initializer is specified explicitly as a part of an attribute.

You can also define a default value inside **Wrapper** itself by providing **wrappedValue** a default value. And if you want a customization, you can define your own `init`function :

```swift
@propertyWrapper
struct Wrapper<T> {
    var wrappedValue: T
    
    init(wrappedValue: T, custom: T) {
        self.wrappedValue = wrappedValue
        /// Other logic
    }
}
```

## Accessing a Property Wrapper

There are three ways to access different parts of a property wrapper: **_wrapper, $wrapper** and **wrapper**:

| Direct Access | What you access |
|-|-|
| wrapper | wrappedValue of property wrapper |
| _wrapper | property wrapper itself |
| $wrapper | projectedValue of property wrapper |

In addition to the wrapped value, a property wrapper can expose additional functionality by defining a projected value. The name of the projected value is the same as the wrapped value, except it begins with a dollar sign ($). You can expose any kind of information through projected value, even property wrapper itself:

```swift
@propertyWrapper
struct Wrapper<T> {
    var wrappedValue: T

    var projectedValue: Wrapper<T> { return self }

    func foo() { print("Foo") }
}
```

Dollar sign is the syntactic sugar to access the wrapper’s projection

```swift
let a = HasWrapper()
a.$x.foo()
```

## Conclusion

Property wrappers is one in every of the foremost exciting new features in Swift 5.1 — because it disclose lots of doors for code reuse and customizability, and enables powerful new ways to implement property-level functionality. This effect is especially drastic when paired with the extensive use of the projectedValue and generics, making property wrappers quite powerful.