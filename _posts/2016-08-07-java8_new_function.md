---
layout: post
title: "Java8新特性详解"
date: 2016-08-07 23:48
categories: [java]
tags: java
---

### 本章要点

> 本章主要讲述java8的新特征，主要包括“闭包”的理解、lambda表达式语法、函数式接口、java.utils.Function包中常用的几个函数式接口、方法引用、接口的默认方法、类库以及编译器方面的优化。

### 一、“闭包”的理解

> 受面向对象编程思想的影响，对与“闭包”这个词一直比较难以理解，于是最近网上查关于它资料，现在也算是有点想法了，下面分享给大家我对闭包的理解。

##### 1 .概念：查找资料感觉，Ruby之父松本行弘在《代码的未来》一书中解释的最好：闭包就是把函数以及变量包起来，使得变量的生存周期延长（这里变量这个词说的比较模糊，我的理解是把闭包体上下文中的局部变量的范围扩大，在闭包体内部任然生效），下面上代码：
{% highlight java linenos %}
//java6开始以这种方式支持闭包
public interface Supplier<T> {
	 T get();
}
public static Supplier<Integer> testClosure(){//注意返回类型
	 final int i = 1;//不用加final关键字编译不报错
	 return new Supplier<Integer>() {//闭包体
		 @Override
		 public Integer get() {
			 return i;//i的生存周期延长
		 }
	 };
}
out：
System.out.print(testClosure().get());
{% endhighlight java linenos %}

> 瞅着下面的函数式接口就是从这儿衍生出来的，只是将称之为函数式接口的编写做了一定的规范，闭包体使用了lambda替代，i变量的定义使用了语法糖，在编译期间将其指定为final类型，从此改善了匿名内部类中变量只能访问局部变量为final修饰的变量。java8中把一些常用的接口改成函数式接口如：Comparator、Runnable等。

##### 2 .使用场景：

> 待续

### 参考致谢
* [lambda表达式和闭包](http://www.importnew.com/17905.html)
* [JAVA8 十大新特性详解](http://www.125135.com/842.htm)
* [Java 8 新特性终极版](http://www.codeceo.com/article/java-8-new-feature.html)
* [JAVA--md5签名文件](http://blog.163.com/wallace0615@126/blog/static/35145824200793151633497/)