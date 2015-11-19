---
layout: post
title: "Programming Scala"
date: 2015-11-17
---

Programming Scala를 읽고 있다.   
따로 찾아보면 좋을 개념들은 따로 정리해둔다.

<br>

### Zero to Sixty: Introducing Scala

- `$javap` reverse engineering
- `function literal` = anonymous function = lambda, closure, block, proc, ...
- `test-double` xUnit Test Patterns: Refactoring Test Code
- Recall Java’s static methods and fields are not tied to an actual instance of some type, whereas Scala objects are single instances of a type.
- `interpolation`
   - [String Interpolation](http://docs.scala-lang.org/overviews/core/string-interpolation.html)

<br>

### Type Less, Do More
- 2.5.4 Nesting Method Definitions and Recursion
  - The JVM and many other language enviorinments don't do **tail-call optimizations**, which would convert a tail-recursive function into a loop. ... Hence, the Scala compiler does limited tail-call optimizations itself.
  - [Why does the JVM still not support tail-call optimization?](http://stackoverflow.com/questions/3616483/why-does-the-jvm-still-not-support-tail-call-optimization/3682044#3682044)
  - [클로저 함수형 프로그래밍의 6가지 원칙](http://autonomist.tistory.com/20)
- `diamond operator` in Java 7
- `Subtype Polymorphism` 하위 타입 다형성, 또는 상속(Inheritance)

  > Polymorphism(다형성) 하나의 메소드나 클래스가 다양한 방법으로 동작하는 것.   
  > - Ad hoc polymorphism => function overloading   
  > - Parametric polymorphism => generics   
  > - Subtyping

- A character with a Unicode value between 0 and 255 may also be represented by an octal escape, i.e., a backslash followed by a sequence of up to three octal characters.
: **조엘 온 소프트웨어** 에 나오는 인코딩 관련 부분 다시 정독할 것.
- 2.9 Option, Some, and None: Avoiding nulls
  - But this is a confusing choice on the language designer's part. Why return a keyword when the programmer expects an instance of a type?
  - Tony Hoarce, who invented the null reference in 1965 while working on a language called ALGOL W, called its invention his "billion dollar" mistake.

<br>

### Rounding Out the Basics
- 1 + 2 vs. 1.+(2)
  - `infix notation` = 인자가 하나뿐인 메서드의 경우 마침표와 괄호 생략
  - 약간 헷갈리는데 메서드 뿐만 아니라 타입에도 적용할 수 있다. 타입을 메서드'처럼' 사용하도록 한 것.
  - [<:<](http://www.scala-lang.org/api/current/#scala.Predef$$$less$colon$less)[A,B] is equivalent to A <:< B

  > If you define a **higher-kinded type** with two type parameters, you can then use that type name as an infix type operator. [(Scala Type Infix Operators)](http://jim-mcbeath.blogspot.kr/2008/11/scala-type-infix-operators.html)

- 1 to String vs. 1.toString
: postfix notation = 인자가 없는 메서드는 마침표 없이 호출 가능 (선택적)
- Use a `for comprehension` when you need to test whether an Option is a Some, in which case you do some work, or is a None, in which case you ignore it.
- `by-name parameter`
   - a function that we call without parentheses
   - defer evaluation until later
   - 예제 from [Occasional Coder](http://locrianmode.blogspot.kr/2011/07/scala-by-name-parameter.html)
   - {% highlight scala %}
def nano() = {
    println("Getting nano")
    System.nanoTime
}
def delayed(t: => Long) = {
    println("In delayed method. Param:"+t)
    t
}
delayed(nano())
{% endhighlight %}

- `trait` interfaces that also give you the option of defining the methods you declare.

<br>

### Pattern Matching
- construction과 extraction의 구문을 일치시키기 위해 +: 는 다음 두 가지일 수 있다.
  - `Seq의 메서드로써의 +:`
  <br>A copy of the sequence/list with an element prepended. [Seq](http://www.scala-lang.org/api/current/#scala.collection.Seq)
  - {% highlight scala %}
scala> val list = 1 +: 2 +: 3 +: 4 +: Nil
list: List[Int] = List(1, 2, 3, 4)
{% endhighlight %}

  - `singleton object인 +:`
  <br>An extractor used to head/tail deconstruct sequences. [object +:](http://www.scala-lang.org/api/current/#scala.collection.$plus$colon$)
  - {% highlight scala %}
list1: Seq[Int] = List(1, 2, 3, 4)
scala> val list1 = Seq(1, 2, 3, 4)
     | list1 match {
     | case head +: tail => // equal to... +:(head, tail)
     | printf("head:%s, tail:%s", head, tail) }
head:1, tail:List(2, 3, 4)
{% endhighlight %}

- `case` keyword used for declaring "special" classes and for case expressions in match expressions.
<br>case classes --> case expressions --> pattern matching
