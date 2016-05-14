---
layout: post
title: "Java编程思想 - 第七章、复用类"
date: 2016-05-07 13:57
categories: [java]
tags: [Java编程思想]
---

### 本章要点

> 本章主要介绍类的复用方式：组合、继承、代理，以及final关键字的使用，还有继承的初始化。

#### 一、 组合、继承、代理

* 组合：解决has-a的关系
* 继承：解决is-a的关系
* 代理：组合与继承之间的中庸之道，尽管java不直接支持代理，但是我们可以模拟其实现过程，比如；我们将一个成员对象置于所要构造的类中（就像组合），但与此同时我们在新类中暴露出该类成员对象的所有方法（就像继承）就像下面的例子：

{% highlight java linenos %}
package chapter7;

class SpaceShipControls {
	void up(int valocity) {}
	void down(int velocity) {}
}

public class Delegation {
	private String name;
	private SpaceShipControls controls = new SpaceShipControls();
    
	public Delegation(String name){
		this.name = name;
	}
	
	//delegation methods
	void up(int valocity) {
		controls.up(valocity);
	}
	void down(int velocity) {
		controls.down(velocity);
	}
	
	public static void main(String[] args){
		Delegation deletation = new Delegation("NSEA Protector");
		deletation.up(100);
	}
}
{% endhighlight java %}

* 是否使用继承：需要**向上转型**的时候我们才考虑使用继承（P140本书作者建议慎用继承）。一般应优先使用组合或者代理，只在确实务必要时才使用继承。因为组合更具灵活性。

#### 二、 继承与初始化

{% highlight java linenos %}
class AA{}

class BB extends AA{
    public static void main(String[] args){
    	BB b = new BB();
    }
}
{% endhighlight java %}

- 在执行**BB.main**的时候，因为main是static方法，就触发了该类的加载（因为Java采用了动态加载技术，只有使用的时候才会被加载）。于是Java解释器调用类加载器在CLASSPATH中找BB.class，如果BB继承自AA，那么编译器注意到BB有一个基类AA（通过extends得到），就开始加载基类，不管你是否打算产生一个该基类的对象（因为需要将该类加载进内存，有其子必有其父是也）。如果该基类继承自其它类，就会以此类推。
- 根基类中的static初始化会被执行，然后是第二个基类，以此类推（如果没有调用new，到这里就结束了）
- 如果使用new创建对象，就会在堆上为该对象分配足够的存储空间
- 存储空间先清空，然后进行默认初始化，基本类型给默认值，引用类型给null。其实这一块是通过将对象内存设为二进制零值一举生成的，说成两个过程是便于理解，本质是分配内存的同时进行设置默认值
- 从根基类开始，执行每个类定义处的域初始化
- 从根基类的构造器开始执行，一直到底

#### 三、 final关键字

* final数据
	* 一个永不改变的编译时常量；对于这种情况，编译器可以把常量带入任何可能用到都额其他计算公式中，也就是说，可以在编译时执行计算式，减轻运行时的负担。在java中这种常量必须是基本类型，定义的时候用final关键字，并且赋值。	
	* 一个在运行时被初始化的值，而你不希望改变它：对于基本类型，final的数值恒定不变；而对于对象引用，final的引用恒定不变（引用被初始化指向一个对象，就无法，再把他指向另一个对象，然而对象自身可以被修改，对数组也适用）
* final方法
	* 原因一：把方法锁住，以防止任何继承类修改它的含义，这是处于设计的考虑，想要考虑在继承中使方法保持不变，并且不会被覆盖
	* 原因二：为了提高效率，在java的早期实现中，将这一关键字指明的方法编译期间会将该方法所有的调用都转为内嵌调用。注意，如果一个方法很大，你的程序会膨胀，因而可能看不到任何性能的提高。
* final类：表明了你不打算继承该类，而且也不允许别人这样做。你对该类的设计并不需要改变，或者是处于安全的考虑，你不希望它有子类。

* final与private
	* 类中所有的private方法都隐式指定为final的，因为无法取用private方法，所以无法覆盖它。就好像构造函数是隐式指定为static类似。
	* 在JAVA中，如果基类有一个方法是private的，那么，在子类中可以使用完全相同的函数（包括所有东西）。因为基类的private方法子类是看不到的，所以你定义一个完全相同的函数子类也会认为是**新函数**，当然不会有问题。但是如果你使用@Override注解，就明确告诉编译器这个方法是覆盖基类的方法，但因为子类“看不到”基类的这个private方法，@Override注解就会报错，说你必须覆盖一个基类存在的方法。