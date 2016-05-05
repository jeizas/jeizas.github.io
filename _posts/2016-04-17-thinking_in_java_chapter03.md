---
layout: post
title: "Java编程思想 - 第三章、操作符"
date: 2016-04-17 00:11
categories: [java]
tags: [Java编程思想]
---

### 本章要点

> 本章主要讲解java中使用的一些操作符，类似C++中的操作符，但是在java中操作符不能重载。虽然看不到未来，但仍然继续。

#### 一、 赋值

* 把一个对象赋值给另一个对象，实际是把对象的“引用”从一个地方复制到另一个地方，这就意味着，假如对象使用c=d，那么c和d都指向原本d指向的那个对象。

{% highlight java linenos %}
class A{
    public Integer a1;
}
class B{
    public static void main(String[] args){
    	A a = new A();
        a.a1 = 1;
        A b = a;
        b.a1 = 2;
        //此时a.a1=2
    }
}
{% endhighlight linenos %}

#### 二、 “==”和“equals”

{% highlight java linenos %}
class A {
	String name;
}
public class B {
	public static void main(String[] args) {
    	A a = new A(); A b = new A();
        System.out.println(a == b); //引用相等
        System.out.println(a.equals(b)); //对象相等
	}
}
{% endhighlight linenos %}

* 引用相等：a和b都是引用，但因为new了2个对象，a和b指向的不是同一个对象，所以这里的结果是false。
* 对象相等：因为A是自定义类型，而且没有重载equals()，将使用Object类的equasl()，实际上调用的还是"=="，也就是判断引用相等。所以结果也是false。

如果想要获得对象相等，先得知道Object类定义的hashCode()和equals()：

> hashCode()的默认行为是对堆上的对象产生一个hash值(一般是根据内存地址计算得到的)。如果你没有重载过hashCode()，不同对象拥有不同的内存，两个对象肯定不可能相等。
    equals()的默认行为是执行"=="的比较。也就是上面说的，比较的是是否指向的都是堆上同一个对象。如果没有重载equals()，默认行为中的两个对象的两个引用肯定不会相同，所以equals()肯定是false。
