---
layout: post
title: "Why does JVM not support tail-call optimization?"
date: 2015-11-24
---

### JIT compiler의 도움이 필요해.
loop으로 바꾸는 간단해보이는 작업을 동적으로 해야만 하는 이유는 무엇일까?   
factorial을 계산하는 두 가지 예제를 보자.   

**Diagnosing Java Code: Improve the performance of your Java code** 에서 가져온 예제다. 코드에 대한 충분한 설명이 부족해서(쪼랩인 내 기준으로...) '내가 이해한 대로' 적음을 미리 밝힌다.

{% highlight java %}
class Example {
  public int product(Iterator i) {
    return productHelp(i, 1);
  }

  int productHelp(Iterator i, int accumulator) {
    if (i.hasNext()) {
      return productHelp(i, accumulator * ((Integer)i.next()).intValue());
    }
    else {
      return accumulator;
    }
  }
}
{% endhighlight %}

{% highlight java %}
public class Example2 extends Example {
  int productHelp(Iterator i, int accumulator) {
    if (accumulator < 1) {
      throw new RuntimeException("accumulator to productHelp must be >= 1");
    }
    else {
      return super.productHelp(i, accumulator);
    }
  }

  public static void main(String[] args) {
    LinkedList l = new LinkedList();
    l.add(new Integer(0));
    new Example2().product(l.listIterator());
  }
}
{% endhighlight %}


Example의 productHelp는 loop으로 바꿀 수 있을 것 같다.

### 참고
- [Diagnosing Java Code: Improve the performance of your Java code](http://web.archive.org/web/20120506085636/http://www.ibm.com/developerworks/java/library/j-diag8/index.html#code_block_1.0)
- [JVM performance optimization, Part 1: A JVM technology primer](http://www.javaworld.com/article/2078623/core-java/jvm-performance-optimization-part-1-a-jvm-technology-primer.html)
