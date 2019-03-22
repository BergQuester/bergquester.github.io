---
layout: post
title: Cleaner, Safer Swift Code Part 1
subtitle: Structs, Enums and Protocols 
tags: [swift, structs, enums, protocols, code-quality]
---

Swift provides many features that make it far more powerful than Objective-C. The Swift community has also developed many tools and best practices as well. Nothing here is new or groundbreaking, yet as part of working with different Swift codebases, I still see programmers ignoring these best practices regularly. In this series, I will discuss a few such features, tools, and techniques for writing cleaner, better code starting with structures, enums, protocols, and extensions.

## Use structs for most of your application state
When using most application frameworks that are rooted in Objective-C or Java, it’s easy to get used to doing everything with classes and objects. The problem is that the use of objects means a shared mutable state can easily worm its way into your code.

The problem here is a shared mutable state means your code will have [hidden side-effects](https://manishearth.github.io/blog/2015/05/17/the-problem-with-shared-mutability/) that are not apparent from the context in which you are currently coding. If these side effects are unknown to the programmer, or even unintended, this will lead to bugs in your code.

In Swift, the answer to avoiding a shared mutable state is to use structs for your state. Structs are value types meaning that setting a struct copies the value rather than a pointer. This behavior may sound slow, but the Swift runtime uses [copy-on-write](https://medium.com/@lucianoalmeida1/understanding-swift-copy-on-write-mechanisms-52ac31d68f2f) for the built-in struct types, such as strings and collections, to optimize structs. You can also implement copy-on-write for your own structs We can’t get away from using objects entirely; we will still need our UILabels, UIButtons, etc. However, for our application state, we can use structs to avoid shared mutable state.

If you’re coming from Objective-C, you probably think that structs are more challenging to work with since they don’t have member methods. Structs in Swift, however, can have member methods, allowing you to use them with an object like syntax:

```swift
struct Computer {
  var name: String
  var price: Int
  var cpuSpeedInMHZ: Int 

  func catalogDescription() -> String {
      let dollars = price / 100
      let cents = price - (dollars * 100)
      return self.name + " $\(dollars).\(cents)"
  }
}
```
There is one major caveat to getting the benefits of value types for avoiding shred mutable state: your structs must be pure value types. This requirement means there _cannot be any reference types_, objects for example, in your structure.


## Use enums when you have one kind of value or another kind of value, but not both

It’s pretty common that you receive one kind of value or another, but not both. The most common case of this is handling a result or an error. The naive way to handle this is to use optionals:

```swift
func handle(result: ServerResult?, error: Error?) {
  guard let error = error else {
    //handle error
    return
  }

  // handle result
}
```

The problem with this is that there is no guarantee that exactly one of the parameters will be populated. How do you make sure that both values are checked? What happens if both are nil? What happens if they are both populated? You’re going to need extra test coverage and code to handle the edge cases.

Wouldn’t it be better if the compiler could enforce this requirement for you? Enter enums:

```swift
enum Result {
  case result(ServerResult)
  case error(Error)
}

func handle(result: Result) {
  switch result {
    case .result(let result):
      // handle result here
    case .error(let error):
      // handle error here
  }
}
```

As you can see, enums in Swift are quite powerful and can contain associated values. We’ve eliminated the optionals, and we have guaranteed that we will get either a result or an error. Additionally, the compiler can check to see if we missed handling a case. 

## Use protocols with extensions
Ok, that’s great and all, but what about sharing code across types? Without inheritance don’t we lose that? Swift has a solution for that: Protocols and Extensions. Take a look at the following example code:

```swift
protocol Product {
  var name: String { get set }
  var price: Int { get set }
  func catalogDescription() -> String
}
```

Here we have created a protocol the requires a first and last name property, both with getters and setters, and a full name method. Any class, struct or enum that adheres to this protocol must implement this interface. Here is our computer struct from before, now conforming to our `Product` protocol:

```swift
struct Computer: Product  {
  var name: String
  var price: Int
  var cpuSpeedInMHZ: Int 

  func catalogDescription() -> String {
      let dollars = price / 100
      let cents = price - (dollars * 100)
      return self.name + " $\(dollars).\(cents)"
  }
}
```

Well, that’s not very impressive, all that we have done is say that `Computer` implements stuff that it already implements. That's more useful than it sounds. Let's say that we have a view controller for displaying product information:

```swift
class ProductViewController: UIViewController {
  var productTitleLabel: UILabel!

  var product: Computer { 
    didSet {
      self.productTitleLabel.text = self.product.catalogDescription()
    }
  }
}
```

Here `product` *must* be a `Computer` struct. However, what if we want to sell other products? This view controller is too tied to `Computer`. Instead, we can tell Swift that `product` is anything that conforms to the `Product` protocol:

```swift
class ProductViewController: UIViewController {
  var productTitleLabel: UILabel!

  var product: Product { 
    didSet {
      self.productTitleLabel.text = self.product.catalogDescription()
    }
  }
}
```

Now the `product` property can be just about anything. Say even a mock object for testing. However, that's a different topic. For now, let's create another product:

```swift
struct Game: Product  {
  var name: String
  var price: Int
  var genre: Genre 

  func catalogDescription() -> String {
      let dollars = price / 100
      let cents = price - (dollars * 100)
      return self.name + " $\(dollars).\(cents)"
  }
}
```

Even though our `ProductViewController` doesn't know anything about `Game`, but because `Game` conforms to the `Product` protocol it will happily accept it. However, now we have a new problem, the `catalogDescription()` method is duplicated between `Computer` and `Game`. This duplication problem is where protocol extensions come in. You may already be aware of extensions to classes, but you can extend protocols too. Let's see how this can help:

```swift
extension Product {
  func catalogDescription() -> String {
      let dollars = price / 100
      let cents = price - (dollars * 100)
      return self.name + " $\(dollars).\(cents)"
  }
}
```

Here we declare an extension to `Product` that implements the same code that we had in our structs. Because the `Product` protocol requires the implementation of the `name` and `price` properties, this extension will automatically work for anything that implements the protocol. With this shared code, we can remove the method from our structures:

```swift
struct Computer: Product  {
  var name: String
  var price: Int
  var cpuSpeedInMHZ: Int 
}

struct Game: Product  {
  var name: String
  var price: Int
  var genre: Genre 
}
```

Finally, our example in its entirety looks like this:

```swift
protocol Product {
  var name: String { get set }
  var price: Int { get set }
  func catalogDescription() -> String
}

extension Product {
  func catalogDescription() -> String {
      let dollars = price / 100
      let cents = price - (dollars * 100)
      return self.name + " $\(dollars).\(cents)"
  }
}

struct Computer: Product  {
  var name: String
  var price: Int
  var cpuSpeedInMHZ: Int 
}

struct Game: Product  {
  var name: String
  var price: Int
  var genre: Genre 
}

class ProductViewController: UIViewController {
  var productTitleLabel: UILabel!

  var product: Product { 
    didSet {
      self.productTitleLabel.text = self.product.catalogDescription()
    }
  }
}
```

One final note about protocol extensions: You don't have to use the default implementation as we are here. If your stuct needs different behavior from the method, it can override it with a custom implementation specific to that struct.

## Conclusion

Hopefully, you can see the benefit of using structs and enums to avoid shared mutable state, how enums can be used to leverage the compiler to guarantee the existence of one value *or* another and how protocols combined with extensions can be used to reduce code between similar types. For more information, see the [Protocol-Oriented Programming in Swift][https://developer.apple.com/videos/play/wwdc2015/408/] session from WWDC 2015 and [Protocol and Value Oriented Programming in UIKit Apps](https://developer.apple.com/videos/play/wwdc2016/419/). Let your inner Crusty out.

Be sure to subscribe to my [feed](https://bergquester.github.io/feed.xml) for the next installment of this series.
