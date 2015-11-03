---
layout: post
title: "Java Primitives and Immutability"
---

'Programming Scala'를 읽는 중인데, val과 var의 차이를 설명하면서 Immutability에 대한 이야기가 나왔다.
Java에도 분명 있는 개념인데 너무 기초여서 학교에서 배운 뒤로 잊었거나... 깊게 생각해본 적 없는 것 같다.   
좀 찾아보다가 그냥 오브젝트에 대해서는 잘 알겠는데 primitives에 대해서 어디에서도 자세히 설명하고 있지 않아서, 나름 이해한 바를 적어본다.

일단 보통 경우의 immutability에 대해서 생각해보자.

`immutable 하다` = 오브젝트가 생성된 이후에 상태를 바꿀 수 없다.  
thread-safe하고 동시성 문제를 피할 수 있다.

'없다!!!' 라는 강제적인 말 때문인지 나는 이상하게 "immutable한 놈들을 mutable하게 쓰면 잘못됨!" 이라고 생각했었다. 하지만 이렇게 맞고 틀림의 문제라기 보다는, Java 오브젝트의 성격 중의 하나라고 생각하면 된다. 그냥 오브젝트들은 mutable하면 mutable한대로, immutable하면 immutable한대로 움직이는 것이다.   
다만 선천적으로 또는 후천적으로 이 성격을 가지고 있냐의 차이는 있다.

- 선천적으로 immutable한 아이들이 바로 `primitive wrapper class`이고
- 후천적으로 immutable한 아이를 만들려면 개발자가 약간 머리를 굴려야 한다.

이쯤에서 내가 이해한 대로 `immutable`의 정의를 약간 바꿔보자면...   
오브젝트가 생성된 이후에 상태(대부분의 경우 값)를 바꿀 수 없기 때문에, 불가피하게 상태를 바꿔야 하면 새로운 놈을 만들어서 아예 reference를 바꾼다.

그럼 다시 내 첫 궁금증인 primitives와의 관계로 돌아가자.   
내 생각은 이랬다: call-by-value 니까 메모리 안에 바로 값이 있을거고... int a = 10 이었다가 a = 5 하면 10 --> 5 로 바뀌니까 mutable 한 것 아닌가????????   
이것에 대한 굉장히 좋은 답은 stackoverflow 에서 찾았다. 


### 참고한 자료들
- [Memory Representation of Primitive Values and Objects](http://www.cs.unc.edu/~dewan/comp401/s09/Class%20Notes/8_Pointers_Notes.pdf)
- [Are Java primitives immutable?](http://stackoverflow.com/questions/18037082/are-java-primitives-immutable)
- [Mutable and Immutable Objects](http://www.javaranch.com/journal/2003/04/immutable.htm)
- [The Laws of Reflection](http://blog.golang.org/laws-of-reflection)
