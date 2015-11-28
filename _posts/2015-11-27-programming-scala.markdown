---
layout: post
title: "Programming Scala"
date: 2015-11-27
---

Programming Scala를 읽고 있다.   
따로 찾아보면 좋을 개념들은 따로 정리해둔다.

<br>

### Zero to Sixty: Introducing Scala

- `$javap` reverse engineering
- `function literal` = anonymous function = lambda, closure, block, proc, ...
- {% highlight java %}
  (s:String) => s.toUpperCase()
  {% endhighlight %}

- `test-double` xUnit Test Patterns: Refactoring Test Code
- Recall Java’s static methods and fields are not tied to an actual instance of some type, whereas Scala objects are single instances of a type.
- `interpolation`
   - [String Interpolation](http://docs.scala-lang.org/overviews/core/string-interpolation.html)

<br>

### Type Less, Do More
- `partial functions` not defined for all possible inputs, defined only for those inputs that match at least one of case clauses.
- Nesting Method Definitions and Recursion
  - The JVM and many other language enviorinments don't do **tail-call optimizations**, which would convert a tail-recursive function into a loop. ... Hence, the Scala compiler does limited tail-call optimizations itself.
  - <b style="color:red">!!!!!!</b> [Why does the JVM still not support tail-call optimization?](http://stackoverflow.com/questions/3616483/why-does-the-jvm-still-not-support-tail-call-optimization/3682044#3682044)
  - <b style="color:red">!!!!!!</b> [클로저 함수형 프로그래밍의 6가지 원칙](http://autonomist.tistory.com/20)
- `Subtype Polymorphism` 하위 타입 다형성, 또는 상속(Inheritance)

  > Polymorphism(다형성) 하나의 메소드나 클래스가 다양한 방법으로 동작하는 것.   
  > - Ad hoc polymorphism => function overloading   
  > - Parametric polymorphism => generics   
  > - Subtyping

- A character with a Unicode value between 0 and 255 may also be represented by an octal escape, i.e., a backslash followed by a sequence of up to three octal characters.
: <b style="color:red">!!!!!!</b> **조엘 온 소프트웨어** 에 나오는 인코딩 관련 부분 다시 정독할 것.
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

<br>

### Implicits
- `implicit conversion`을 사용하면, 다른 부분을 건드리지 않고도 새로운 함수를 추가할 수 있다!   
: 즉, Expression Problem을 해결할 수 있다.
  - `Expression Problem` the desire to extend modules without editing their source code
  - OOP에서는 상속으로 이 문제를 해결한다. & [Open/Closed Principle](https://ko.wikipedia.org/wiki/%EA%B0%9C%EB%B0%A9-%ED%8F%90%EC%87%84_%EC%9B%90%EC%B9%99)   
  ==> 하지만, 사용하지 않는 코드가 남아있거나 부모의 변화가 모든 자식에게 영향을 미칠 수 있는 문제가 있다.   
  ==> 그래서 나온 것이 `Single Responsibility Principle` 하나의 관심사를 유지시켜!   
  ==> 하지만(2)! 실제로는 두 가지 클래스를 '믹스'해야 하는 경우가 많이 생긴다.  
  ==> 이럴 때는 Scala에서는 `trait`를 사용하자.
  - *metaprogramming* 을 대신해서는, **typed class** 를 사용한다.
  - <b style="color:red">!!!!!!</b> [The SOLID Principles](http://code.tutsplus.com/series/the-solid-principles--cms-634)

<br>

### Functional Programming in Scala
- FP offers effective techniques for three major challenges of our time:
  - the need for pervasive concurrency (Immutability와 강하게 연결된다.)
  - the need to write data-centric applications
  <br>요즘 유명한 프로젝트로는 역시 Apache Spark인데 Scala를 지원한다.
  - the need to write bug-free applications
- **What Is Functional Programming?**
  - `reference transparency`(참조투명성)

  > An expression is said to be referentially transparent if it can be replaced with its value without changing the behavior of a program. (여기까지 잘 이해 안됨) In other words, yielding a program that has the same effects and output on the same input. [(wikipedia)](https://en.wikipedia.org/wiki/Referential_transparency)

    - 참조투명성이 보장되면... 함수가 계산한 값을 다른 놈으로 대체할 수 있다.   
    ==> 함수나 변수나 다루는게 또이또이!   
    ==> Functions are `first-class value` in FP, just like data values.
    - `higher-order function` 함수 파라미터를 받거나 함수를 리턴하는 함수
- "It does encourage you to write your code this way whenever possible."
- Although tail-call self-recursion optimizations are not yet supported natively by the JVM, scalac will attempt them.
- "Functional programs put greater emphasis on the use of a core set of data structures and algorithms compared to object languages."
