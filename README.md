# Enhatch - Swift Style Guide

This document contains a set of recommended guidelines, conventions, and best pracitices for writing code in our iOS code base. Strict adherence to the guide is enforced to improve readability and consistancy. Remever that code is read much more often than it is written, and that 80% of the lifetime cost of software goes to maintainence. The idea is to keep a set of guidelines for everyone to follow in order to make the code more consistent, understandable, and less prone to the unobvious pitfalls.

**Note:** Suggestions for improvements are highly encouraged. 

**Note:** Apple is generally right. Defer to Apple's preferred or demonstrated way of doing things. You should follow the style of Apple's code as defined within their [“The Swift Programming Language”](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html#//apple_ref/doc/uid/TP40014097-CH3-ID0) book wherever possible. However Apple is a large corporation and be prepared to see discrepancies in their example code.

## Line Length

Each line should have a maximum of **120** characters. To be able to handle this limit, set a page guide at column 120. You can do this by going to Prefences->Text Editing

## Spacing

**Indent using 2 spaces** rather than tabs to conserve space and help prevent line wrapping. Be sure to set this preference in Xcode as shown below

Make sure you have **Automatically trim trailing whitespace** and **Including whitespace-only lines** enabled in Xcode.

There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but having too many sections in a method often means you should refactor into several methods.

**Tip:** You can re-indent by selecting some code (or ⌘A to select all) and then Control-I (or Editor\Structure\Re-Indent in the menu). Some of the Xcode template code will have 4-space tabs hard coded, so this is a good way to fix that.

## Naming

Use descriptive names with camel case for classes, methods, variables, etc. Class names should be capitalized, while method names and variables should start with a lower case letter.

**Preferred:**

```swift
private let maximumWidgetCount = 100

class WidgetContainer {
  var widgetButton: UIButton
  let widgetHeightPercentage = 0.85
}
```

**Not Preferred:**

```swift
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
  var wBut: UIButton
  let wHeightPct = 0.85
}
```

For functions and init methods, prefer named parameters for all arguments unless the context is very clear. Include external parameter names if it makes function calls more readable.

```swift
func dateFromString(dateString: String) -> NSDate
func convertPointAt(column column: Int, row: Int) -> CGPoint
func timedAction(afterDelay delay: NSTimeInterval, perform action: SKAction) -> SKAction!

// would be called like this:
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(afterDelay: 1.0, perform: someOtherAction)
```

For methods, follow the standard Apple convention of referring to the first parameter in the method name:

```swift
class Counter {
  func combineWith(otherCounter: Counter, options: Dictionary?) { ... }
  func incrementBy(amount: Int) { ... }
}
```

### Enumerations

Use UpperCamelCase for enumeration values:

```swift
enum Shape {
  case Rectangle
  case Square
  case Triangle
  case Circle
}
```

When writing switch statments, scope the cases and avoid using a default or catch-all (e.g. don’t use default just to omit the last case). When possible, default should be an error case.

### Class Prefixes

Swift types are automatically namespaced by the module that contains them and you should not add a class prefix. If two names from different modules collide you can disambiguate by prefixing the type name with the module name.

```swift
import SomeModule

let myClass = MyModule.UsefulClass()
```

## Comments

### General Commenting

Use comments to provide additional information about a function that isn't clearly understood from the function's name.

**Tip:** A good rule of thumb is to consider the amount of time it takes for a person with zero context to understand/modify your code with vs. without comments. 

**Tip:**  When you find yourself writing a lot of comments about a chunk of code, you should also consider rearchitecting or breaking down the code.

### Documentation

All internal and public methods must have documentation comments. This allows other developers to have more context when they need to access a class/function they didn't write. It also allows us to use tools to autogenerate documention pages to make it easier for engineers to understand code they didn't write. More detail on the swift documentation can be found on this [NSHipster post](http://nshipster.com/swift-documentation/).

```swift
/**
	Removes a single separator at the beginning and/or end of a string if it is the first/last character
	
	- parameter string: The string to modify
	- returns: A string with a single separator removed from the beginning and/or end if it is the first/last character
 */
```

### MARK/TODO/FIXME

Use MARKs to divide functionarionality into meaningful, easy-to-navigate sections. 

```
// MARK: UITableviewDelegate
```

Use TODOs and FIXMEs to label code that has yet to be done, needs additonal functionality, or to document a hack that should be handled differently when there is more time.
All TODOs and FIXMEs should follow the following format ```TODO:(<name>) <date> <description>```

```
// TODO:(ed) 12-20-2015 Finish the style guide!
```

## Imports

Imports should be separated into two segments with a space between them and should be sorted alphabetically.

- Apple Frameworks
- Third Party Frameworks

```
import CoreData
import UIKit

import Fabric
import Watchdog
```

## Classes and Structures

### Which one to use?

Remember, structs have [value semantics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_144). Use structs for things that do not have an identity. An array that contains [a, b, c] is really the same as another array that contains [a, b, c] and they are completely interchangeable. It doesn't matter whether you use the first array or the second, because they represent the exact same thing. That's why arrays are structs.

Classes have [reference semantics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_145). Use classes for things that do have an identity or a specific life cycle. You would model a person as a class because two person objects are two different things. Just because two people have the same name and birthdate, doesn't mean they are the same person. But the person's birthdate would be a struct because a date of 3 March 1950 is the same as any other date object for 3 March 1950. The date itself doesn't have an identity.

Sometimes, things should be structs but need to conform to `AnyObject` or are historically modeled as classes already (`NSDate`, `NSSet`). Try to follow these guidelines as closely as possible.

### Example definition

Here's an example of a well-styled class definition:

```swift
class Circle: Shape {
  var x: Int, y: Int
  var radius: Double
  var diameter: Double {
    get {
      return radius * 2
    }
    set {
      radius = newValue / 2
    }
  }

  init(x: Int, y: Int, radius: Double) {
    self.x = x
    self.y = y
    self.radius = radius
  }

  convenience init(x: Int, y: Int, diameter: Double) {
    self.init(x: x, y: y, radius: diameter / 2)
  }

  func describe() -> String {
    return "I am a circle at \(centerString()) with an area of \(computeArea())"
  }

  override func computeArea() -> Double {
    return M_PI * radius * radius
  }

  private func centerString() -> String {
    return "(\(x),\(y))"
  }
}
```

The example above demonstrates the following style guidelines:

 + Specify types for properties, variables, constants, argument declarations and other statements with a space after the colon but not before, e.g. `x: Int`, and `Circle: Shape`.
 + Define multiple variables and structures on a single line if they share a common purpose / context.
 + Indent getter and setter definitions and property observers.
 + Don't add modifiers such as `internal` when they're already the default. Similarly, don't repeat the access modifier when overriding a method.


### Use of Self

For conciseness, avoid using `self` since Swift does not require it to access an object's properties or invoke its methods.

Use `self` when required to differentiate between property names and arguments in initializers, and when referencing properties in closure expressions (as required by the compiler):

```swift
class BoardLocation {
  let row: Int, column: Int

  init(row: Int, column: Int) {
    self.row = row
    self.column = column
    
    let closure = {
      println(self.row)
    }
  }
}
```

### Protocol Conformance

When adding protocol conformance to a class, prefer adding a separate class extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.

**Preferred:**

```swift
class MyViewcontroller: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewcontroller: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewcontroller: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

**Not Preferred:**

```swift
class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

### Computed Properties

For conciseness, if a computed property is read-only, omit the get clause. The get clause is required only when a set clause is provided.

**Preferred:**

```swift
var diameter: Double {
  return radius * 2
}
```

**Not Preferred:**

```swift
var diameter: Double {
  get {
    return radius * 2
  }
}
```

## Function Declarations

Keep short function declarations on one line including the opening brace:

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

For functions with long signatures, add line breaks at appropriate points and add an extra indent on subsequent lines:

```swift
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
    translateConstant: Int, comment: String) -> Bool {
  // reticulate code goes here
}
```

Make methods class methods if they don't depends on instance state (e.g. if the method doesn't use any instance variables). This makes it much easier to reason through a method if there is no need to think about the states it can be in.

## Closure Expressions

Use trailing closure syntax only if there's a single closure expression parameter at the end of the argument list. Give the closure parameters descriptive names.

**Preferred:**

```swift
UIView.animateWithDuration(1.0) {
  self.myView.alpha = 0
}

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  },
  completion: { finished in
    self.myView.removeFromSuperview()
  }
)
```

**Not Preferred:**

```swift
UIView.animateWithDuration(1.0, animations: {
  self.myView.alpha = 0
})

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  }) { f in
    self.myView.removeFromSuperview()
}
```

For single-expression closures where the context is clear, use implicit returns:

```swift
attendeeList.sort { a, b in
  a > b
}
```


## Types

Always use Swift's native types when available. Swift offers bridging to Objective-C so you can still use the full set of methods as needed.

**Preferred:**

```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**Not Preferred:**

```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

In Sprite Kit code, use `CGFloat` if it makes the code more succinct by avoiding too many conversions.

### Constants

Constants are defined using the `let` keyword, and variables with the `var` keyword. Always use `let` instead of `var` if the value of the variable will not change.

**Tip:** A good technique is to define everything using `let` and only change it to `var` if the compiler complains!


### Optionals

Declare variables and function return types as optional with `?` where a nil value is acceptable.

Use implicitly unwrapped types declared with `!` only for instance variables that you know will be initialized later before use, such as subviews that will be set up in `viewDidLoad`.

When accessing an optional value, use optional chaining if the value is only accessed once or if there are many optionals in the chain:

```swift
self.textContainer?.textLabel?.setNeedsDisplay()
```

Use optional binding when it's more convenient to unwrap once and perform multiple operations:

```swift
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```

When naming optional variables and properties, avoid naming them like `optionalString` or `maybeView` since their optional-ness is already in the type declaration.

For optional binding, shadow the original name when appropriate rather than using names like `unwrappedView` or `actualLabel`.

**Preferred:**

```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, volume = volume {
  // do something with unwrapped subview and volume
}
```

**Not Preferred:**

```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```

**Note:** Avoid ```!``` where possible

In general prefer ```if let```, ```guard let```, and ```assert``` to ```!```, whether as a type, a property/method chain, ```as!```, or ```try!```. It’s better to provide a tailored error message or a default value than to crash without explanation. Design with the possibility of failure in mind.

As an author, if you do use ```!```, consider leaving a comment indicating what assumption must hold for it to be used safely, and where to look if that assumption is invalidated and the program crashes. Consider whether that assumption could reasonably be invalidated in a way that would leave the now-invalid ```!``` unchanged.

As a reviewer, treat ```!``` with skepticism.

### Struct Initializers

Use the native Swift struct initializers rather than the legacy CGGeometry constructors.

**Preferred:**

```swift
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
let centerPoint = CGPoint(x: 96, y: 42)
```

**Not Preferred:**

```swift
let bounds = CGRectMake(40, 20, 120, 80)
let centerPoint = CGPointMake(96, 42)
```

Prefer the struct-scope constants `CGRect.infinite`, `CGRect.null`, etc. over global constants `CGRectInfinite`, `CGRectNull`, etc. For existing variables, you can use the shorter `.zero`.

### Type Inference

Prefer compact code and let the compiler infer the type for a constant or variable, unless you need a specific type other than the default such as `CGFloat` or `Int16`.

**Preferred:**

```swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = [String]()
let maximumWidth: CGFloat = 106.5
```

**Not Preferred:**

```swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
var names: [String] = []
```

**NOTE**: Following this guideline means picking descriptive names is even more important than before.


### Syntactic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

**Preferred:**

```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**Not Preferred:**

```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```


## Control Flow

Prefer the `for-in` style of `for` loop over the `for-condition-increment` style. 

Braces go on the same line as the conditions.

**Preferred:**

```swift
for _ in 0..<3 {
  println("Hello three times")
}

for (index, person) in attendeeList.enumerate() {
  println("\(person) is at position #\(index)")
}
```

**Not Preferred:**

```swift
for var i = 0; i < 3; i++ {
  println("Hello three times")
}

for var i = 0; i < attendeeList.count; i++ {
  let person = attendeeList[i]
  println("\(person) is at position #\(i)")
}
```

### Early Returns & Guards
When possible, use ```guard``` statements to handle early returns or other exits (e.g. fatal errors or thrown errors).

**Preferred:**

```swift
guard let safeValue = criticalValue else {
    fatalError("criticalValue cannot be nil here")
}
someNecessaryOperation(safeValue)
```

**Not Preferred:**

```swift
if let safeValue = criticalValue {
    someNecessaryOperation(safeValue)
} else {
    fatalError("criticalValue cannot be nil here")
}
```

**Also Not Preferred:**

```swift
if criticalValue == nil {
    fatalError("criticalValue cannot be nil here")
}
someNecessaryOperation(criticalValue!)
```

This flattens code otherwise tucked into an ```if let``` block, and keeps early exits near their relevant condition instead of down in an ```else``` block.

Even when you're not capturing a value (```guard let```), this pattern enforces the early exit at compile time. In the second ```if``` example, though code is flattened like with guard, accidentally changing from a fatal error or other return to some non-exiting operation will cause a crash (or invalid state depending on the exact case). Removing an early exit from the ```else``` block of a ```guard``` statement would immediately reveal the mistake.

## Semicolons

Swift does not require a semicolon after each statement in your code. They are only required if you wish to combine multiple statements on a single line.

Do not write multiple statements on a single line separated with semicolons.

The only exception to this rule is the `for-conditional-increment` construct, which requires semicolons. However, alternative `for-in` constructs should be used where possible.

**Preferred:**

```swift
let swift = "not a scripting language"
```

**Not Preferred:**

```swift
let swift = "not a scripting language";
```

**NOTE**: Swift is very different to JavaScript, where omitting semicolons is [generally considered unsafe](http://stackoverflow.com/questions/444080/do-you-recommend-using-semicolons-after-every-statement-in-javascript)

## Language

Use US English spelling to match Apple's API.

**Preferred:**

```swift
let color = "red"
```

**Not Preferred:**

```swift
let colour = "red"
```

## Extensions for Code Organization
Extensions should be used to help organize code.

## Best Practices

### Keep your methods small and focused
Try to keep your methods short and easy to reason about. Break methods up by functionalities!

### Name your methods by the functionalities they provide
Method names should reflect what they do instead of what is calling them.

Never write code merely to attempt to reduce the number of keystrokes you need to type. Rely on autocompletion, autosuggestion, copy and paste, etc instead. Verbosity is often helpful to other maintainers of your code. That said, being overly verbose can bypass one of Swift's key benefits: type inference.

### Expose as little as possible in public interfaces
Consumers of a class should only be concerned with what the class promises to do. The more internal details you expose, the more things are likely to break when you modify the implementation of your code.  Marking a definition as "private" or "internal" can act as lightweight documentation for your code. Anyone reading the code will know that these elements are "hands off". Conversely, marking a definition as "public" is an invite for other code to access the marked elements. It is best to be explicit and not rely on Swift's default access control level ("internal").

It is far easier to change the access control of your code to be more permissive later (along the spectrum: "private" to "internal" to "public") as needed. Code that has too permissive access control might be used inappropriately by other code. Making code more restrictive could involve finding the inappropriate or incorrect uses and providing better interfaces. This is a trying to close the stable door after the horse has bolted style problem. An example of this could be a type exposing an internal cache publicly.

If your codebase grows in the future, it may end being broken down into sub-modules. Doing so on a codebase already decorated with access control information is much quicker and easier.

**Tip:** When possible, properties should be readonly. Or better yet, don’t expose them at all!

### Avoid boolean traps when creating APIs
When creating new methods, try to make it so readers do not have to dig into the documentation or implementation to figure out what something means.

For instance, do NOT do stuff like: ```def refresh(animated: BOOL)```; BAD. Although it is clear in the implementation that the flag is referring to whether the refresh must happen with animation, in the caller side, you will see code that looks like this: ```foobar.refresh(true)``` BAD, where it is impossible to figure out by reading what the parameter refer to.

To mitigate this, you should name your methods so the word prior to the param indicates what it is expecting. To be even clearer, you may also use an enum for the parameter. e.g. ``` def refresh(withTransitionType transitionType: FoobarTransitionType)```.

A pretty good read on this subject: [hall of api shame: boolean trap.](http://ariya.ofilabs.com/2011/08/hall-of-api-shame-boolean-trap.html)

### Document what your code does not support
It is impossible to design things to support every foreseeable use case, but you should try to document any known issues. For instance, if a view controller does not lay out its subviews properly in landscape mode, leave a comment so people wishing to reuse it later will know.

### Reduce scope of variables
When reading code, it is usually best if variables are declared and used in the smallest scopes possible, so it is easier to read and reason through the effects of the variables (where they are used, modified, etc.).

### Always inject dependancies
If your class relies on something to handle network requests, for example, then why not pass that into the ```init``` function?
