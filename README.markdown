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
