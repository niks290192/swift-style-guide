# Swift Style Guide.
### Updated for Swift 4.2

This style guide is different from others you may see because the focus is centered on readability for print and the web. We created this style guide to keep the code in our books, tutorials, and starter kits nice and consistent - even though we have many different authors working on the books.

Our overarching goals are clarity, consistency, and brevity, in that order

## Table of Contents

* [Correctness](#correctness)
* [Naming](#naming)
  * [Prose](#prose)
  * [Delegates](*delegates)
  * [Use Type Inferred Context](#use-type-inferred-context)
  * [Generics](#generics)
  * [Class Prefixes](#class-prefixes)
  * [Language](#language)
* [Code Organization](#code-organization)
    * [Protocol Conformance](#protocol-conformance)
    * [Unused Code](#unused-code)
    * [Minimal Imports](#minimal-imports)
* [Spacing](#spacing)
* [Comments](#comments)
* [Classes and Structures](#classes-and-structures)
    * [Use of Self](#use-of-self)
    * [Protocol Conformance](#protocol-conformance)
    * [Computed Properties](#computed-properties)
    * [Final](#final)
* [Function Declarations](#function-declarations)
* [Function Calls](#function-calls)
* [Closure Expressions](#closure-expressions)
* [Types](#types)
    * [Constants](#constants)
    * [Static Methods and Variable Type Properties](#static-methods-and-variable-type-properties)
    * [Optionals](#optionals)
    * [Lazy Initilization](#lazy-initilization)
    * [Type Inference](#type-inference)
    * [Syntatic Sugar](#syntatic-sugar)
* [Functions vs Methods](#functions-vs-methods)
* [Memory Management](#memory-management)
    * [Extending Lifetime](#extending-lifetime)
* [Access Control](#access-control)
* [Control Flow](#control-flow)
    * [Ternary Operator](#ternary-operator)
* [Golden Path](#golden-path)
    * [Failing Guards](#failing-guards)
* [Semicolons](#semicolons)
* [Parentheses](#parentheses)
* [Multi-line String Literals](#multi-line-string-literals)
* [No Emoji](#no-emoji)
* [Organization and Bundle Identifier](#organization-and-bundle-identifier)
* [Copyright Statement](#copyright-statement)
* [Smiley Face](#smiley-face)
* [References](#references)


## Correctness

Strive to make your code compile without warnings. This rule informs many style decisions such as using `#selector` types instead of string literals.

## Naming

Descriptive and consistent naming makes software easier to read and understand. Use the Swift naming conventions described in the [API Design Guidelines](https://swift.org/documentation/api-design-guidelines/). Some Key takeaways include:

- striving for clarity at the call site
- Prioritizing clarity over brevity 
- using camel case (not snake case)
- using uppercase for types (and protocols), lowercase for everything else
- including all needed words while omitting needless words 
- using names based on roles, not types
- sometimes compensating for weak type information
- striving for fluent usage 
- beginning factory methods with `make`
- naming methods for their side effects 
  - verb methods follow the -ed, -ing rule for the non-mutating version
  - noun methods follow the formX rule for the mutating version
  - boolean types should read like assertions
  - protocols that describe _what something is_ should read as nouns
  - protocols that describe _a capability_ should end in _-able_ or _-ible_
- using terms that don't surprise experts or confuse beginners
- generally avoiding abbreviations 
- using precedent for names
- preferring methods and properties to free functions 
- casing acronyms and initialisms uniformly up or down
- giving the same base name to methods that share the same meaning 
- avoiding overloads on return type 
- choosing good parameter manes that serve as documentation
- preferring to name the first parameter instead of including its name in the method name, except as mentioned under Delegates
- labeling closure and tuple parameters
- taking advantage of default parameters

### Prose

When referring to methods in prose, being unambiguous is critical. To refer to a method name, use the simplest form possible.

1. Write a method name with no parameters. **Example:** Next, you need to call `addTarget`.
2. Write the method name with argument labels. **Example:** Next, you need to call `addTarget(_:action)`.
3. Write the full method name with argument labels and types.
**Example:** Next, you need to call `addTarget(_: Any?, action: Selector?)`.

For the above example using `UIGestureRecognizer`, 1 is unambiguous and preferred.

**Pro Tip:** You can use Xcode's jump bar to lookup methods with argument labels. If you're particularly good at mashing lots of keys simultaneously, put the cursor in the method name and press **Shift-Control-Option-Command-C** (all 4 modifier keys) and Xcode will kindly put the signature on your clipboard.

![Methods in Xcode jump bar](screens/xcode-jump-bar.png)

### Class Prefixes

Swift types are automatically namespaced by the module that contains them and you should not add a class prefix such as RW. if two names from different modules collide you can disambiguate by prefixing the type name with the module name. However, only specify the module name when there is a possibility for confusion which should be rare.


```swift
import SomeModule

let myClass = MyModule.UsefulClass()
```

### Delegates 

When creating custom delegate methods, an unnamed first parameter should be the delegate source. (UIKit contains numerous example of this.)

**Preferred**:
```swift 
func namePickerView(_ namePickerView: NamePickerView, disSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

**Not Preferred**
```swift
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShould() -> Bool
```

### Use Type Inferred Context

Use compiler inferred context to write shorter, clean code. (Also see[Type Inference](#type-inference).)


**Preferred**:
```swift
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```

**Not Preferred**:
```swift
let selector = #selector(ViewController.viewDidload)
view.backgroundColor = .red
let toView = context.view(forKey: UITransitionContextViewKey.to)
let view = UIView(frame: CGrect.zero)
```

### Generics 

Generic type parameters should be descriptive, upper camel case names. When a type name doesn't have a meaningful relationship or role, use a traditional single uppercase letter such as `T`, `U`, or `V`.

**Preferred**:
```swift
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

**Not Preferred**
```swift
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

### Language

Use US English spelling to match Apple's API.

**Preferred**
```swift
let color = "red"
```

**Not Preferred**
```swift
let color = "red"
```

## Code Organization

Use Extensions to organize your code into logical blocks of functionality. Each extension should be sent off with a `// MARK: -`
comment to keep things well-organized.

### Protocol Conformance

In particular, when adding protocol conformance to a model, prefer adding a separate extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.

**Preferred**:
```swift 
class MyViewController: UIViewController {
    // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
    // tableview data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
    // scroll view delegate methods
}
```

**Not Preferred**
```swift 
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
    // all methods
}
```

Since the compiler does not allow you to redeclare protocol conformance in a derived class, it is not always required to replicate the extension groups of the base class. This is especially true if the derived class is a terminal class and a small number of methods are being overridden. When to preserve the extension groups is left to the discretion of the author.

For UIKit view controllers, consider grouping lifecycle, custom accessors, and IBAction in separate class extensions.


### Unused Code

Unused (dead) code, including Xcode template code and placeholder comments should be removed. An exception is when your tutorial or book instructs the user to use th commented code.

Aspirational mentods not directly associated with the tutorials whose implementation simply calls the superclass should also be removed. This includes any empty/ununsed UIApplicationDelegate methods. 

**Preferred**:
```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return Database.contacts.count
}
```

**Not Preferred**:
```swift
override func didReceiveMemoryWarning() {
    super.didReceiveMemoryWarning()
    // Dispose of any resources that can be recreated.
}

override func numberOfSections(in tableView: UITableView) -> Int {
    // #waring Incomplete implememtaion, return the number of sections
    return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsINSection section: Int) -> Int {
    // #waring Incompleted Implementation, retunr the number if rows
    return Database.contacts.count
}
```

### Minimal Imports

Import only the modules a source file requires. For example, don't import `UIKit` when importing `Foundation` will suffice. LikeWise, don't import `Foundation` if you must import `UIKit`

**Preferred**:
```
import UIKit
var view: UIView
var deviewModels: [String]
```

**Preferred**:
```
import Foundation
var deviceModels: [String]
```

**Not Preferred**:
```
import UIKit
import Foundation
var view: UIView
var deviceModels: [String]
```

**Not Preferred**
```
import UIKit
var deviceModels: [String]
```

## Spacing
* Indent using 2 spaces rather than tabs to conserve space and help prevent line wrapping. Be sure to set this perference in Xcode and in the Project setting as shown below:

![Xcode indent setting](screens/indentation.png)

* Method braces and other braces (`if`/ `else` / `switch` / `while` etc.)
always open to the same line as the statement but close on a new line.

* Tip: You can re-indent by selecting some code for (or **Command-A** to select all) and then **Control-I** (or **Editor ▸ Structure ▸ Re-Indent** in the menu). Some of the Xcode template code will have 4-space tabs hard coded, so this is a good way to fix that.

**Preferred**:
```swift
if user.isHappy {
    // Do something
} else {
    // Do something
}
```

**Not Preferred**:
```swift
if user.isHappy 
{
    // Do something
} 
else 
{
    //Do something else
}
```

* There should be excatly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should seperate functionality, but having too many too sections in a method often means you should refactor into  sevral methods.

* There should be no blank lines after an opening brace or before a closing brace.

* Colons always have no space on the left and one space on the right. Exceptions are the ternary operator `? :`, empty dictionary `[:]` and `#selector` syntax `addTarget(_:action:)`.

**Preferred**:
```swift
class TestDatabase: Database {
    var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
} 
```

**Not Preferred**:
```swift
class TestDatabase : Database {
    var data :[String:CGFloat] = ["A" : 1.2, "B": 3.2]
}
```

* Long lines should be wrapped at around 70 characters. A hard linit is intentionally not specified.

* Avoid trailing whitespaces at the ends of lines.

* Add a single newline character at the end of each file.


## Comments

When they needed, use comments to explain **ehy** a praticular piece of code does something. Comments must be kept up-to-date or deleted.

Avoid block comments inline with code, as the code should be as self documenting as possible. _Exception: This does not apply to those comments used to generate documentation_

Avoid use of C-style comments (`/* ... */`). Prefer the use of double- or triple-slash.


## Classes and Structures

### Which one to use?

Remember, structs have [value sementics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_144).
Use structs for things that can do not have an identity. An array that contains [a, b, c] is really the same as another array that contains [a, b, c] and they are completely interchangeable. It doesn't matter whether you use the first array or the second, because they represent the excat same thing. That's why array are structs.

Classes have [reference semantics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_145).
Use classes for things that do have an identity or a specific life cycle. You would model a person as a class because two person objects are two different things. Just because two people have the same  name and birthdte, doen't mean they are the person. But the person's birthdate would be a struct because a date of 3 March 1950 is the same as any other date object for march 3 March 1950 is the same as any other date object for 3 March 1950. The date ifself doesn't have an identity.

Sometimes, things should be structs but need to conform to `AnyObject` or are historically modeled as classes already [`NSDate`, `NSSet`]. Try to follow these guidelines as closely as possible.


### Example definition 

Here's an example of a well-styled class definition:


```swift 
class Circle: SHape {
    var x: Int, y: Int
    var redius: Double
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

    override func area() -> Double {
        return Double.pi * radius * radius
    }
}

extension Circle: CustomStringConveryible {
    var description: String {
        return "center = \(centerString) area = \(area))"
    }

    private var centerString: String {
        return "(\(x), \(y))"
    }
}
```

The example above demonstrates the following style guidelines:

+ Specify types for properties, variables, constants, argument declarations and other statements with a space after the colon but not before, e.g. `x: Int`, and `Circle: Shape`.
+ Define multiple variables and structure on a single line if they share a common purpose / context.
+ Indent getter and setter definitations and property observers.
+ Don't add modifiers such as `internal` when they're already the default. Similarly, don't repeat the access modifier when overriding a method. 
+ Organize extra functionality (e.g. printing) in extensions.
+ Hide non-shared, implementation details such as `centerString` inside the extension using `private` access control.


### Use of Self

For conciseness, avoid using `self` since Swift does not require it to access an object's properties or invoke its methods.

Use self only when require by the compiler (in `@escaping` closures, or in initializers to disambiguate properties from arguments). In other words, if it compiles without `self` then omit it.

### Computed Properties

For conciseness, if a computed property is read-only, omit the get clause. The get clause is required only when a set clause is provided.

**Preferred**:
```swift
var diameter: Double {
    return radius * 2
}
```

**Not Preferred**:
```swift
var diameter: Double {
    get {
        return radius * 2
    }
}
```

### Final

Marking classes or members as `final` in tutorials can distract from the main topic and is not required. Nevertheless, use of `final` can sometimes clarify your intent and is worth the cost. In the below example, `Box` has a particular purpose and customization in a derived class is not intended. Marking it `final` makes that clear.

```swift
// Turn any generic type into a reference type using this box class.
final class Box<T> {
    let value: T
    init(_ value: T){
        self.value = value
    }
}
```

## Function Declarations

Keep short functions declarations on one line including the opening brace:

```swift
func reticulateSplines(spline: [Double]) -> Bool {
    // reticulate code goes here
}
```

For functions with long signatures, put each parameter on a new line and add an extra intend on subsequent lines:

```swift
func reticulateSplines(
    spline: [Double], 
    adjustmentFactor: Double, 
    translateConstant: Int, comment: String
) -> Bool {
    // reticulate code goes here
}
```

Don't use `(Void)` to represent the lack of an input; simply use `()`.
Use `Void` instead of `()` for closure and function outputs.

**Preferred**

```swift
func updateConstraints() -> Void {
    // magic happens here
}

typealias CompletionHandler = (result) -> Void
```

## Function Calls

Mirror the style of function declarations at call sites. Calls that fit on a single line should be written as such:

```swift
let success = reticulateSplines(splines)
```

If the call site must be wrapped, put each parameter on a new line, indented one additional level:

```swift
let success = reticulateSplines(
    spline: splines,
    adjustmentFactor: 1.3,
    translateConstant:2, 
    comment: "normalize the display")
```

## Closure Expressions

Use trailing closure syntax only of there's a single closure expression parameter at the end of the argument list. Give the closure parameter descriptive names.

**Preferred**:
```swift
UIView.animated(withDuration: 1.0) {
    self.myView.alpha = 0
}

UIView.animated(withDuration: 1.0, animation: {
    self.myView.alpha = 0
}, completion:{ finished in
    self.myView.removeFromSuperView()
})
```

**Not Preferred**:
```swift
UIView.animated(withDuration: 1.0, animations:{
    self.myView.alpha = 0
})

 UIView.animated(withDuration: 1.0, animations:{
    self.myView.alpha = 0
 }) { f in
    self.myView.removeFromSuperView()
 }
```

For single-expression closure where the context is clear, use implicit returns:

```swift
attendeeList.sort { a, b in
    a > b
}
```

Chained methods using trailing closure should be clear and easy to read in context, decisions on spacing, line breaks, and when to user named versus anonymus arguments is left to the descretion of the author. 
Examples:

```swift
let value = number.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = number
    .map{$0 * 2}
    .filter {$0 > 50}
    .map{ $0 + $10}
```

## Types

Always use Swift's native types and expressions when available . Swift offers bridging to Objective-C so you can still use the full set of methods as needed.

**Preferred**:
```swift
let width = 120.0                                           //Double
let widthString = "\(width)"                                //String
```

**Less Preferred**:
```swift
let width = 120.0                                           //Double
let widthString = (width as NSNumber).stringValue           //String
```

**Not Preferred**:
```swift
let width: NSNumber = 120.0                                 //NSNumber
let widthString: NSString = width.stringValue               //NSString
```

In drawing code, use `CGFloat` if it makes the code more succinct by avoiding too many conversions.
### Constants

Constants are defined using the `let` keyword and variable with the `var` keyword. Always use `let` instead of `var` if the value of the variable will not change.

**Tip:** A good techinque is to define everything using `let` and only cjange it to `var` if the compiler complains!

You can define constants on a type rather than on an instance of that type using type properties. to declare a type property as a conatant simply use `static let`. Type properties declared in this way are generally preferred over global constants because they are easier to distinguish from instance properties. Example:

**Preferred**:
```swift
enum Math {
    static let e = 2.718281828459045235360287
    static let root2 = 1.41421356237309504880168872
}

let hypotenuse = side * Math.root2
```

**Note:** The advantage of using a case-less enumeration is that it can't accidentally be instantiated and works as a pure namespace.

**Not Preferred**:
```swift
let e = 2.718281828459045235360287 // pollutes global namespace
let root2 = 1.41421356237309504880168872

let hypotenuse = side * root2 // what is root2?
```

### Static Methods and Variable Type Properties

Statis methods and type properties work similarly to global functions and global variables and should be used sparingly. They are useful when functionaluty is scoped to a particular type or when Objective-C interoperability is required.


### Optionals 

Declare variables and function return types as optionals with `?` where a `nil` value is acceptable.

Use implicitly unwrapped types declared with `!` only for instance variables that you will be initialized later before use, such as subviews that will be setup in `viewDidLoad()`. Prefer optional binding to implicitly unwrapped optionals in most other cases.

Wehn accessing an optional value, use optional chaining if the value is only accessing once or if there are many optionals in the chain: 

```swift
textContainer?.textLabel?.setNeedsDisplay()
```

Use optional binding when it's more convenient to unwrap once and preform multiple operations:

```swift
if let textContainer = textContainer {
    // do many things with textContainer
}
```


When naming optional variables and properties, avoid naming them like `optionalString` or `maybeView` since their optional-ness is already in the type declaration.

For optionals binding, shadow the original name whenever possible rather than using names like `unwrappedView` or `actualLabel`.

**Preferred**:
```swift
var subview: UIView?
var volume: Double?

// later on....
if let subview = subview, let voulme = volume {
    // do something with unwrapped subview and volume
}

// another example
UIView.animate(withDuration: 2.0) { [weak self] in
    guard let self = self else { return }
    self.alpha = 1.0
}
```


**Not Preferred**:
```swift 
var optionalSubView: UIView?
var volume: Double?

if let unwrappedSubView = optionalSubView {
    if let realVolume = volume {
        // do something with unwrappedSubView and RealVolume
    }
}

// another example
UIView.animate(withDuration: 2.0) { [weak self] in 
    guard let strongSelf = self else { return }
    strongSelf.alpha = 1.0
}
```


###  Lazy Initialization

Consider using lazy initialization for finer grained control over object lifetime. This is especially true for `UIViewController` that loads views lazily. You can either use a closure that is immediately called `{}()` or call a private factory method. Example:

```swift
lazy var locationManager = makeLocationManager()

private func makeLocationManager() -> CLLocationManager {
    let manager = CLLocationManager()
    manager.desiredAccuracy = kCLLocationAccuracyBest
    manager.delegate = self
    manager.requestAlwaysAuthorization()
    return manager
}
```

**Notes:**
  - `[unowned self]` is not required here. A retain cycle is not created. 
  - Location manager has a side-effect for popping up UI to ask user for permission so fine grain control makes sense here.


### Type Inference

Prefer compact code and let the compiler infer the type for constants or variables of single instances. Type Inference is also appropriate for small, non-empty arrays and dictionaries. When required specify the specific type such as `CGFloat` or `Int16`.

**Preferred**:
```swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = ["Mic", "Sam", "Christine"]
let maximumwidth: CGFloat = 106.5
```

**Not Preferred**:
```swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
var names = [String]()
```

#### Type Annotation for Empty Arrays and Dictionaries

For empty arrays and dictionaries, use type annotation. (For an array or dictionary assigned to a large, multi-line literal, use type annotation.)


**Preferred**:
```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```

**Not Preferred**:
```swift
var names = [String]()
var lookup = [String: Int]()
```

**NOTE**: Following this guideline means picking descriptive names is even more important than before.

### Syntatic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

**Preferred**:
```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**Not Preferred**
```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

## Functions vs Methods

Free functions, which aren't attached to a class or type, should be used sparingly. When possible, prefer to use a method instead of a free function. This aids in readibility and discoverability.

Free functions are most appropriate when they aren't associated with any particular type or instance.

**Preferred**:
```swift
let sorted = items.mergeSorted() // easily discoverable
rocket.launch() //acts on the model
```

**Not Preferred**:
```swift
let sorted = mergeSort(items) // hard to discover
launch(&rocket)
```

**Free Functions Exceptions**
```swift
let tuples = zip(a, b) // feels natural as a free function(symmetry)
let value = max(x, y, z) //another free function that feels natural
```

## Memory Management

Code (even non-production, tutorial demo code) should not create reference cycles. Analyze your object graph and prevent strong cycles with `weak` and `unowned` references. Alternativelym use value types (`struct`, `enum`) to prevent cycles altogether.

### Extending object lifetime

Extend object lifetime using the `[weak self]` and `guard let self = self else { return }` idiom. `[weak self]` is preferred to `[unowned self]` where it is not immediately obvious that `self` outlives the closure. Explicitly extending lifetime is preferred to optional chaining.

**Preferred**:
```swift
resource.request().onComplete{ [weak self] response in
    guard let self = self else {
        return
    }
    let model = self.updateModel(response)
    self.updateUI(model)
}
```

## Access Control

Full access control annotation in tutorials can distract from the main topic and is not required. Using `private` anc `fileprivate` appropriately, however, adds clarity and promotes encapsulation. Prefer `private` to `fileprivate`; use `fileprivate` only when the compiler insists.

Only explicitly use `one`, `public`, and `internal` when you require a full access control are the `static` sprcifier or attributes such as `@IBAction`, `@IBOutlet` and `@discardableResult`.

**Preferred**:
```swift
private let message = "Great Scott!"

class TimeMachine {
    private dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```


**Not Preferred**:
```swift
fileprivate let message = "Great Scott!"

class TimeMachine {
    lazy dynamic private var fluxCapacitor = FluxCapacitor()
}
```

## Control Flow

Prefer the `for-in` style of `for` loop over the `while-condition-increment` style.

**Preferred**:
```swift
for _ in 0..<3 {
    print("Hello three times")
}

for (index, person) in attachmentList.enumerated() {
    print("\(person) is at position #\(index)")
}

for index in stride(form: 0, to: items.count, by: 2) {
    print(index)
}

for index in (0...3).reversed() {
    print(index)
}
```

**Not Preferred**:
```swift
var i = 0
while i < 3 {
    print("Hello three times")
    i += 1
}

var i = 0
while i < attendeeList.count {
    let person = attendeeList[i]
    print("\(person) is at position #\(i)")
    i += 1
}
```

### Ternary Operator

The Ternary operator, `?:` , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an `if` statement or refactored into instance variable. In general, the best use of the ternary operator is during assignment of a variable and deciding which value to use.

**Preferred**:

```swift
let value = 5
result = value != 0 ? x : y

let isHorizontal = true
result = isHorizontal ? x : y
```

**Not Preferred**:
```swift
result = a > b ? x = c > d ? c : d : y
```

## Golden Path
when coding with conditionals, the left-hand margin of the code should be the "golden or "happy" path. That is, don't nest `if` statements. Multiple return statements are OK. The `guard` statement is build for this.

**Preferred**:
```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
    guard let context = context else {
        throw FFTError.noContext
    }
    guard let inputData = inputData else {
        throw FFTError.noInputData
    }

    //use context and input to compute the frequencies
    return frequencies
}
```

**Not Preferred**:
```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {
    if let context = context {
        if let inputData = inputData {
            //use context and input to compute the frequencies 
            return frequencies 
        } else {
            throw FFTError.noInputData
        }
    } else {
        throw FFTError.noContext
    }
}
```

When Multiple coptionals are unwrapped either with `guard` or `if let`, minimize nesting by using the compund version when possible. In the compund version, place the `guard` on its own line, then ident each condition on its own line. The `else` clause is indented to match the conditions and the code is indented on additional level, as shown below. Example:

**Preferred**:
```swift
guard
    let number1 = number1, 
    let number2 = number2,
    let number3 = number3
    else {
        fatalError("impossible")
}
// do something with numbers
```

**Not Preferred**:
```swift
if let number1 = number1 {
    if let number2 = number2 {
        if let number3 = number3 {
            //do something with numbers
        } else {
            fatalError("impossible")
        }
    } else {
        fatalError("Impossible")
    }
} else {
    fatalError("Impossible")
}
```

### Failing Guards

Guard statements are required to exit in someway. Generally, this should be simple one line statement such as `return`, `throw`, `break`, `continue`, and `fatalError()`. Large code blocks should be avoided. If cleanup code is required for multiple exit points, consider using a `defer` block to avoid cleanup code duplication.

## SemiColons

Swift does not require a semicolon after each statement in your code. They are only required if you wish to combine multiple statements on a single line.

Do not write multiple statements on a single line separated with semicolons.

**Preferred**:
```swift
let swift = "not a scripting language"
```

**Not Preferred**:
```swift
let swift = "not a scripting language";
```

**NOTE**: Swift is very different from JavaScript, where omitting semicolons is [generally considered insafe](http://stackoverflow.com/questions/444080/do-you-recommend-using-semicolons-after-every-statement-in-javascript)

##Parantheses

Parantheses around conditionals are not required and should be omitted.

**Preferred**:
```swift
if name == "Hello" {
    print("World")
}
```

**Not Preferred**:
```swift
if (name == "Hello") {
    print("World")
}
```

In larger expressions, optionals parentheses can sometimes make code read more clearly.

**Preferred**:
```swift
let playerMark = (player == current ? "X" : "0")
```

## Multi-line String Literals

When building a long string, you're encourged to use the multi-line string literal syntax. Open the literal on the same line as the assignment but do not include text on that line. Indent the text block one additional level.

**Preferred**:
```swift
let message = """
    You cannot charge the flux \
    capacitor with a 9V battery.
    You must use a super-charger \
    which costs 10 credits. You currently \
    have \(credits) credits available.
    """
```

**Not Preferred**:

```swift
let message = """You cannot charge the flux \
  capacitor with a 9V battery.
  You must use a super-charger \
  which costs 10 credits. You currently \
  have \(credits) credits available.
  """
```

**Not Preferred**:

```swift
let message = "You cannot charge the flux " +
    "capacitor with a 9V battery.\n" + 
    "You must use a super-charger " +
    "which costs 10 credits. You currently " +
    "have \(credits) credits available." 
```

## No Emoji

Do not use emoji in your projects. For those readers who actually type in their code, it's an unnecessary source of friction. while it may be cute, it does't add to the learning and it interrupts the coding flow for these readers.

## Organization and Bundle Identifier

Where an Xcode project is involved, the organization should be set to `Orgnization Name` and the bundle Identifier set to `com.organizationname.ExampleProject` where `ExampleProject` is the name of the tutorial project.

![Xcode Project settings](screens/project_settings.png)