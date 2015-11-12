---
layout: post
title: "Java Primitives and Immutability"
date: 2015-11-12
---

'Programming Scala'를 읽는 중에, val과 var의 차이를 설명하면서 Immutability에 대한 이야기가 나온다.   
Java에도 분명 있는 개념인데 너무 기초여서 학교에서 처음 배운 뒤로 잊었거나... 깊게 생각해본 적 없는 것 같다.   
좀 찾아보다가 그냥 오브젝트에 대해서는 잘 알겠는데 primitives에 대해서 어디에서도 자세히 설명하고 있지 않아서, 나름 이해한 바를 적어본다.

<br>

일단 일반적인 경우의 immutability에 대해서 생각해보자.   

`immutable 하다` 오브젝트가 생성된 이후에 상태를 바꿀 수 없다.  

**'없다'** 라는 강제적인 말 때문인지 나는 **"immutable한 놈들을 mutable하게 쓰면 잘못됨!"** 이라고 생각했었다. 하지만 이렇게 맞고 틀림의 문제라기 보다는, Java 오브젝트의 성격 중의 하나라고 생각하면 된다. 그냥 오브젝트들은 mutable하면 mutable한대로, immutable하면 immutable한대로 움직이는 것이다.   

이렇게 개념을 바꿀 수 있었던 계기가 된 stackoverflow 답변은 이렇다.

> Immutable means that each time the value of and object has changed a new reference is created for it on stack.

예제도 쉽게 만들 수 있다.
{% highlight java %}
String s1 = "Hello";
String s2 = s1;
// s1 and s2 now point at the same string - "Hello"

s1 = "Help!";
System.out.println(s2); // still prints "Hello"
{% endhighlight %}

s1은 **바뀌어야 하기 때문에** s1의 레퍼런스는 바뀌었지만, s2의 레퍼런스는 바뀌지 않았다.

그렇다면, 정의를 약간 바꿔볼 수 있다.   
`immutable 하다` *오브젝트가 생성된 이후에 상태를 바꿀 수 없고,* 상태를 바꿔야 하는 경우 새로운 상태를 가진 오브젝트를 생성해서 참조한다.

<br>

다만 선천적으로 또는 후천적으로 이 성격을 가지고 있냐의 차이는 있다.

- 선천적으로 immutable한 아이들 중 일부가 흔한 예로 나오는 primitive wrapper class.
- 후천적으로 immutable한 아이를 만들려면 개발자가 주의가 필요하다.

<br>

그럼 다시 내 원래 궁금증인 primitives와의 관계로 돌아가자.

primitive type에 대해 immutability를 논하지 않는 다는 것은 잘 안다. 위처럼 immutability를 자연스럽게 받아들이고 메모리 할당을 이해하게 되면, 너무 당연한 소리라서 아무도 논하지 않는 것 같다. 결국 나만 모르고 있었다는 뜻 ㅠㅠ

`int i = 5;` 가 실행되면 어떻게 메모리에 5가 들어가는지 생각해본 적 있는지?

<img src="/assets/storing_integer_in_memory.png" style="width: 50%;"/>

1. int형이 들어갈 수 있는 두 개의 메모리 블럭을 할당한다.
<br> 하나는 i를 위한 variable block이고, 또 하나는 5를 가진 value block이다.
2. value block의 값을 variable block 에 복사한다.

이것은 흐름이 얼마나... 새로 정의한 immutability와 너무 일맥상통한지! primitive type은 immutable한 것은 너무 당연해서 어찌할 수 없는 그런 것. ~허무~

아직 모르는게 너무 많다. 속속들이 다 알고 남들처럼 멋있게 글도 쓰고 싶은데.

<br>

### 참고
- stackoverflow
  - [Are Java primitives immutable?](http://stackoverflow.com/questions/18037082/are-java-primitives-immutable)
  - [Immutability of Strings in Java](http://stackoverflow.com/questions/1552301/immutability-of-strings-in-java)
- [Memory Representation of Primitive Values and Objects](http://www.cs.unc.edu/~dewan/comp401/s09/Class%20Notes/8_Pointers_Notes.pdf)
<br> 우연히 찾은 대학교 강의 자료로, 나처럼 메모리까지 생각해본 적 없다면 정말 추천한다.
- [Mutable and Immutable Objects](http://www.javaranch.com/journal/2003/04/immutable.htm)
- [The Laws of Reflection](http://blog.golang.org/laws-of-reflection)
<br> go에 대한 내용이지만 일맥상통하는 부분이 있다. 어려워서 아직 다 이해하진 못했다.
