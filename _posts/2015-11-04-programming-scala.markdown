---
layout: post
title: "Programming Scala"
date: 2015-11-04
---

Programming Scala를 읽고 있다.   
따로 찾아보면 좋을 개념들은 따로 정리해둔다.

### Zero to Sixty: Introducing Scala

- `$javap` reverse engineering
- `function literal` = anonymous function = lambda, closure, block, proc, ...
- `test-double` xUnit Test Patterns: Refactoring Test Code
- Recall Java’s static methods and fields are not tied to an actual instance of some type, whereas Scala objects are single instances of a type.
- `interpolation` from Scala 2.10

### Type Less, Do More
2.5 Method Declarations
  2.5.4 Nesting Method Definitions and Recursion

- The JVM and many other language enviorinments don't do tail-call optimizations, which would convert a tail-recursive function into a loop. ... Hence, the Scala compiler does limited tail-call optimizations itself.

- `diamond operator` in Java 7
