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

> 在JAVA中，闭包可以通过“接口+内部类”来实现，因为对于非静态内部类而言，它不仅记录了其外部类的详细信息，还保留了一个创建非静态内部类的引用，通过它可以访问外部类的私有成员，因此可以把非静态内部类当成面向对象领域的闭包。那么，通过这种仿闭包的非静态内部类可以很方便地实现回调，这是一种非常灵活的功能。

### 二、lamdba表达式

* 基本语法

{% highlight java linenos %}
	(parameters) -> expression 或(parameters) ->{ statements; }
{% endhighlight java linenos %}

* 返回类型：函数式接口（下面介绍）
* 作用域: 在lambda表达式中访问外层作用域和老版本的匿名对象中的方式很相似。你可以直接访问标记了final的外层局部变量，或者实例的字段以及静态变量。

### 三、函数式接口

* 概念：众所周知在java中，每个方法或者表达式都对应一个类型，那么问题来了：lamdba表达式对应的类型是什么呢？答案很简单是一种听起来熟悉又陌生的创新接口“函数式接口”。看这个名字归根结底它也是一种接口（interface），每一个lambda表达式都对应一个类型，通常是接口类型。
* 定义“函数式接口”是指仅仅只包含一个抽象方法的接口，每一个该类型的lambda表达式都会被匹配到这个抽象方法。因为 默认方法 不算抽象方法，所以你也可以给你的函数式接口添加默认方法。
* 实例：
{% highlight java linenos %}
package functionalIngerface;

/**
 * 函数式接口，@FunctionalInterface编译器自动检测是否符合函数式接口
 */
@FunctionalInterface
public interface TestInterface<I, O> {
	
	public O toInteger(I i);//java接口public、abstract、static
	
	default void defultMethod(){
		System.out.println("default method...");
	}
}

package functionalIngerface;

public class Test {
	
	static TestInterface<String, Integer> convert = (x) -> {return Integer.valueOf(x);};
	
	public static void main(String[] args){
		Integer intVal = convert.toInteger("2");//函数式接口中的方法
		convert.defultMethod();//默认方法
		System.out.println(intVal);
	}
}
{% endhighlight java linenos %}

### 四、JDK 1.8 API的常用函数式接口

> 在老Java中常用到的比如Comparator或者Runnable接口，这些接口都增加了@FunctionalInterface注解以便能用在lambda上。Java 8 API同样还提供了很多全新的函数式接口来让工作更加方便

* Predicate接口：接口只有一个参数，返回boolean类型
* Function 接口：接口有一个参数并且返回一个结果，并附带了一些可以和其他函数组合的默认方法（compose, andThen）
* Supplier 接口：接口返回一个任意范型的值，和Function接口不同的是该接口没有任何参数
* Consumer 接口；接口表示执行在单个参数上的操作。
* Comparator 接口：是老Java中的经典接口， Java 8在此之上添加了多种默认方法：
* Optional 接口：不是函数是接口，这是个用来防止NullPointerException异常的辅助类型
* Filter 过滤
* Stream 接口
* Sort 排序
* Map 映射
* Match 匹配
* Count 计数
* Reduce 规约
* 并行Streams
* Map

{% highlight java linenos %}
public class Lamdba {
	
	static Function<String, Integer> toInteger = (x)-> Integer.valueOf(x);
	static Predicate<String> isNull = (x) ->  x == null;
	static Supplier<String> testSupplier = () -> new String("supplier");
	static Consumer<String> greeter = (s) -> System.out.println("Hello, " + s);
//	static Optional<String> optional = Optional.of("abcdefg");
	
	public static void main(String[] args){
		System.out.println(toInteger.apply("2"));
		System.out.println(isNull.test(null));
		System.out.println(testSupplier.get());
		greeter.accept("consumer");
		
		List<String> strs = Arrays.asList("a", "c", "e", "f");
		Collections.sort(strs, (s1, s2) -> s2.compareTo(s1));
		strs.stream().forEach(System.out::print);
		
		Optional.of("abcdefg").ifPresent((s) -> System.out.println("\n" + s.charAt(0)));
	}
}
{% endhighlight java linenos %}

### 五、新API

> 待续

### 参考致谢

* [lambda表达式和闭包](http://www.importnew.com/17905.html)
* [JAVA8 十大新特性详解](http://www.125135.com/842.htm)
* [Java 8 新特性终极版](http://www.codeceo.com/article/java-8-new-feature.html)
* [JAVA--md5签名文件](http://blog.163.com/wallace0615@126/blog/static/35145824200793151633497/)