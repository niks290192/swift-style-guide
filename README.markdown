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