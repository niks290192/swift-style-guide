# Swift Style Guide.
### Updated for Swift 4.2

This style guide is different from others you may see, because the focus is centered on readability for print and the web. We created this style guide to keep the code in our books, tutorials, and starter kits nice and consistent - even through we have many different authors working on the books.

Our overarching goals are clarity, consistency and bervity, in that order

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

Descriptive and consistent naming makes software easier to read and understand. Use the Swift naming conventions described in the [API Design Guidelines](https://swift.org/documentation/api-design-guidelines/). Some Key takeways include:

- striving for clarity at the call site
- Prioritizing clarity over brevity 
- using camel case (not snamke case)
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
- generally avoiding abbrevations 
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

1. Write method name with no parameters. **Example:** Next, you need to call `addTarget`.
2. Write the method name with argument labels. **Example:** Next, you need to call `addTarget(_:action)`.
3. Write the full method name with argument labels and types.
**Example:** Next, you need to call `addTarget(_: Any?, action: Selector?)`.

For the above example using `UIGestureRecognizer`, 1 is unambiguous and preferred.

**Pro Tip:** You can use Xcode's jum bar to lookup methods with argument labels. If you're particularly good at mashing lots of keys simultaneously, put the cursor in  the method name and press **Shift-Control-Option-Command-C** (all 4 modifier keys) and Xcode will kindly put the signature on your clipboard.

![Methods in Xcode jump bar](screens/xcode-jump-bar.png)

### Class Prefixes

Swift types are automatically namespaced by the module that contains them and you should not add a class prefix such as RW. if two names from different modules collide you can disambiguate by prefixing the type name with the module name. However, only specify the module name when there is possibility for confusion which should be rare.


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