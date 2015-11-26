---
layout: post
title: "Why does JVM not support tail-call optimization?"
date: 2015-11-24
---

공부해가면서 쓰는 Java Optimization.

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
역시 stackoverflow에는 모든 질문이 있다! 나보다 똑똑한 누군가의 답변은 이렇다.

> As explained by Brian Goetz (Java Language Architect at Oracle) in this video:   
> in jdk classes [...] there are a number of **security sensitive methods that rely on counting stack frames** between jdk library code and calling code to figure out who's calling them.
Anything that changed the number of frames on the stack would break this and would cause an error.   
> [(Why doesn't Java have optimization for tail-recursion at all?)](http://programmers.stackexchange.com/questions/272061/why-doesnt-java-have-optimization-for-tail-recursion-at-all)

음. 질문부터 읽어보면 질문자는 나와 비슷한 글을 본 것 같다. "상속되면 이러쿵저러쿵 복잡할 수 있지만... static method는 상관없는거 아니야?" 그런데 답변을 보니까 잘 이해가 안 간다. stack이랑 도대체 무슨 상관이란 말인가! TCO가 일어나면 이 놈이 바뀌고 그래서 보안 문제가 생긴다 이건가?

### 참고
- [JVM performance optimization, Part 1: A JVM technology primer](http://www.javaworld.com/article/2078623/core-java/jvm-performance-optimization-part-1-a-jvm-technology-primer.html)
