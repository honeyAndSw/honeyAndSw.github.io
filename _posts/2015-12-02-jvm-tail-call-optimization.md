---
layout: post
title: "Why does JVM not support tail-call optimization?"
date: 2015-12-02
---

> Although tail-call self-recursion optimizations are not yet supported natively by the JVM, scalac will attempt them.

이 한 줄 때문에 시작하게 된 공부해가면서 쓰는 Java Optimization.

<br>

### JIT compiler의 도움이 필요해.
[**Diagnosing Java Code: Improve the performance of your Java code**](http://web.archive.org/web/20120506085636/http://www.ibm.com/developerworks/java/library/j-diag8/index.html#code_block_1.0) 에 의하면, loop으로 바꾸는 간단해보이는 작업일지라도 정적 컴파일이 코드의 결과를 바꾸는 경우가 있다. 정적 컴파일러인 javac가 하기에는 상황에 따라 헷갈린다는 말씀.

factorial을 계산하는 두 가지 예제를 보자. (위 페이지에서 가져온 예제인데, 짧게 수정했다.)   

{% highlight java %}
class Example {
  public int product(Iterator i) { return productHelp(i, 1);}

  int productHelp(Iterator i, int accumulator) {
    if (i.hasNext()) {
      return productHelp(i, accumulator * ((Integer)i.next()).intValue());
    }
    return accumulator;
  }
}
{% endhighlight %}

`productHelp`는 tail recursive한 부분이라서 loop으로 바꿀 수 있을 것 같다.   
하지만 조금 있다가 바꾸기로 하고, Example을 상속한 Example를 보자.

{% highlight java %}
public class Example2 extends Example {
  int productHelp(Iterator i, int accumulator) {
    if (accumulator < 1) {
      throw new RuntimeException("accumulator to productHelp must be >= 1");
    }
    return super.productHelp(i, accumulator);
  }

  public static void main(String[] args) {
    LinkedList l = new LinkedList();
    l.add(new Integer(0));
    new Example2().product(l.listIterator());
  }
}
{% endhighlight %}

코드 실행 결과는 어떨까? Example2.productHelp에 두 번째 들어갔을 때 `accumulator = 0`이 되어 RuntimeException이 발생한다.   
이것을 기억해두고, Example.productHelp를 loop으로 바꿔보자.

{% highlight java %}
class Example {
  public int product(Iterator i) { return productHelp(i, 1);}

  int productHelp(Iterator i, int accumulator) {
    while (i.hasNext()) {
      accumulator =  accumulator * ((Integer)i.next()).intValue();
    }
    return accumulator;
  }
}
{% endhighlight %}

엄청 좋아보이고 최적화한 것 같다! 그런데 문제가 있다 ㅠㅠ 이렇게 코드가 바뀌면 loop으로 바꾸기 전과 실행 결과가 달라진다. `accumulator < 1`을 확인하는 부분이 없어졌기 때문에 RuntimeException이 발생하지 않고 프로그램이 끝나버린다.   

내가 이해한대로 정리해보자면, 핵심 로직인 tail recursion은 loop으로 바꿀 수 있지만, 자식 클래스에서 무슨 짓을 하고 있는지에 따라서 단지 loop으로 바꿨을 뿐인데 결과 자체가 달라질 수도 있다는 거다.

<br>

### JIT compiler가 해주면 되는거 아니야?
역시 stackoverflow에는 모든 질문이 있다! 나보다 먼저 궁금해한 누군가가 있다.   
[(Why doesn't Java have optimization for tail-recursion at all?)](http://programmers.stackexchange.com/questions/272061/why-doesnt-java-have-optimization-for-tail-recursion-at-all)

> why doesn't Java at least have tail-recursion optimization for static methods and enforce proper way to call static methods with the compiler?

음. 질문을 읽어보면 질문자는 나와 비슷한 상태인 것 같다:   
"상속되면 이러쿵저러쿵 복잡할 수 있지만... static method는 상관없는거 아니야?"

> As explained by Brian Goetz (Java Language Architect at Oracle) in this video:   
> in jdk classes [...] there are a number of **security sensitive methods that rely on counting stack frames** between jdk library code and calling code to figure out who's calling them.
Anything that changed the number of frames on the stack would break this and would cause an error.

그런데 답변은 바로 이해가 안 간다. stack이랑 security가 갑자기 왜 등장하는걸까.

recursion을 loop으로 바꾸는 것을 스택에서 생각해보면, 스택에 쌓여있는 A return A return A ... 함수 호출들이 모두 뿅 줄어드는 것이다. 그러면 스택에서 알 수 있던 callee-caller의 관계같은 것들이 처음과 달라지고, 이 스택을 지켜보고 있던 일부 Java API들이 영향을 받게 된다. 주로 access check(나쁜 callee가 caller를 함부로 호출하지는 않았는지)과 stack tracing 때문이라고.   

이 access check가 말그대로 '보안'과 관련된 것인지는 모르겠으나, 예를 들어

- java.lang.Class.\*
- java.lang.reflect.Method.\*
- java.lang.reflect.Constructor.\*
- java.security.AccessController.\*

같은 API에서 문제가 될 수 있다고 한다.   
이 내용은 John Rose가 쓴 [tail calls in the VM](https://blogs.oracle.com/jrose/entry/tail_calls_in_the_vm)를 읽고 나름대로 정리해본 것으로, 이 글을 아직 정확하게 이해하는 것은 아니라서 틀릴 수도 있다!

Java8에서 큰 변화들이 많았기 때문에 TCO도 곧 추가되지 않겠냐 하는 논의가 진행중이지만 아직은 '글쎄...' 의 반응이 더 우세한 것 같다.

<br>

### 참고
- [JVM performance optimization, Part 1: A JVM technology primer](http://www.javaworld.com/article/2078623/core-java/jvm-performance-optimization-part-1-a-jvm-technology-primer.html)
- [Does the JVM prevent tail call optimizations?](http://stackoverflow.com/questions/105834/does-the-jvm-prevent-tail-call-optimizations)
