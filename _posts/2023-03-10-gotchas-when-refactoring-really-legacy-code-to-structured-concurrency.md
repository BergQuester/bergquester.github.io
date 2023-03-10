---
layout: post
title: Gotchas When Refactoring Really Legacy Code to Structured Concurrency
subtitle: Hardware and software recommendations for video and audio
tags: [swift, structured-concurrency, legacy-code]
---
Recently, I have been working on migrating some Swift ansynchronous code to Swit Structured Concurrency. I'm working in a large codebase that dates back to when Swift was just a twinkle in Chris Lattner's eye.

With years of developers rolling on and off the project and much code written in Objective-C and pre-`Result` type, I have encountered a few gotchas. Here I will go through some of the special considerations a developer needs to keep in mind when brining Structured Concurrency into an older codebase.

## Callback Closure Contract Violations

With completion handlers, there is a contract that is expected with how they are called: The callback closure will be called exactly once and will have either a value on success or an error value on failure. Swift Structured Concurrency enforces this contract, while legacy code may violate them. With violations, it is important to study the code to understand the intended semantics, if any, of the callback closure issues that you encounter.

### Invalid Calback Values

In legacy code you might find closures that predate the `Result` type and are instead of the form `(Success?, Error?)`. The issue here is that there is nothing enforcing one and only one value being populated. The following is a table showing the possible values in for this tuple and their validity with the standard callback contract:

| Success | Failure | Valid |
|:-------:|:-------:|:-----:|
| nil     | nil     | No    |
| nil     | Error   | Yes   |
| Value   | nil     | Yes   |
| Value   | Error   | No    |

I have seen `(nil, nil)` passed in some scenarios, while I have not encountered both values being populated.

In either case, updating the closures to a `Result` type is a good intermediate step before updating the function to be `async` with structured concurrency. When doing so, take care to examine the *symantics* of the invalid cases. You may need to introduce additional `Error` cases that are ignored up the call chain or make accomodations for parital `Success`.

### One and Only One Callback Call

When calling a callback closure function from an `async` method it is necessary to wrap the call in one of the several `withContinuation` variants. All variants expect that a `resume()` variant is called on the `Continuation` exactly once. `withCheckedContinuation` variants will enforce this at compile time while `withUnsafeContinuation` will still expect this contract to be fulfilled, but the compiler will not enforce it. The following table shows the validity of the number of calls:

| Number of Calls | Valid |
|:---------------:|:-----:|
| 0               | No    |
| 1               | Yes   |
| 2 or more       | No    |

With legacy callback closure code, there is no enforcement of this callback contract. Legacy methods may either call the callback more than once, or not at all. In the first case a workaround may be to use mechanisms to ensure that the `Continuation` is only called once.

In both cases, the broken function should ultimately be updated to conform to the callback contract. Again, pay attention to the semantics of the callback abuse and make additional adjustments in client code as needed.

## MainActor Issues

### Add @MainActor Annotations
Some code may access views, view controllers, or other resources that are required to be on the main thread. Make sure to annotate any functions or `Tasks` that do such work with `@MainActor`. For example:

```swift
// A function that runs on the `MainActor`
@MainActor
func asyncFunction() async {
//...
}
```

```swift
// A `Task` that runs on the `MainActor`

Task { @MainActor in
//...
}
```

There is also a [bug in Swift](https://forums.swift.org/t/xcode-14-1-withcheckedcontinuations-body-will-run-on-background-thread-in-case-of-starting-from-main-actor/60953) where `withContination` wrappers don't always inherit the current async context. Supposedly this was fixed in a recent version of Xcode, but I have still seen issues. A workaround is to create a `MainActor` `Task` within the `withContination` wrapper like so:

```swift
await withContinuation { continuation in
    Task { @MainActor
        // Call callback closure function here
    }
}
```

### Objective-C Considerations 

`async` Swift functions can be exposed to Objective-C as a method that takes a completion closure:

```swift
@objc
func structuredConcurrencyMethod() async {}
```

```objectivec
- (void)objectiveCMethod {
    [object structuredConcurrencyMethodWithCompletionHandler:^() {
        // Handle callback
    }];
```

Objective-C doesn't have the concept of a `MainActor`. If you have work that needs to be done on the main thread, use GCD to dispatch to the main queue:

```objectivec
- (void)objectiveCMethod {
    [object structuredConcurrencyMethodWithCompletionHandler:^() {
        dispatch_async(dispatch_get_main_queue(),^{
            // Do main thread work
        });
    }];
```

### Refactor 

If possible, try to refactor the view and resource access out of async code and into client code by communicating results up the call chain.

## Documentation

## When to Refactor to Structured Concurrency?

    Moving to structured concurrency can cause complicated and subtle issues if the callback issues above -- and wider architectural issues -- are not first addressed. Therefore, it is highly recommended that refactoring to structured concurrency be a later refactor after the larger issues have been addressed.
