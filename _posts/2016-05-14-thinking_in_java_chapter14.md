---
layout: post
title: "Java编程思想 - 第十四章、类型信息"
date: 2016-05-14 20:04
categories: [java]
tags: [Java编程思想]
---

### 本章要点

> 这一章主要告诉讲**程序在运行时发现和使用类信息**。主要有两种方式：一种是传统的**RTTI**（Run-Time Type Indentification）；另一种是**反射机制**。前者是在便伊始就知道类的所有信息，一个简单的例子：多态的动态绑定就是用的RTTI获取类信息的，而后者.class是在编译时不可获取的，只在运行时打开和检查.class文件。

#### 一、 RTTI

##### 1. 从实例中认识RTTI

{% highlight java linenos %}
import java.util.*;

abstract class Shape {
    void draw() {System.out.println(this + ".draw()"); }
    abstract public String toString();
}

class Circle extends Shape {
    public String toString() { return "Circle"; }
}

class Triangle extends Shape {
    public String toString() { return "Trangle"; }
}

class Square extends Shape {
    public String toString() { return "Square"; }
}

public class Shapes {
    public static void main(String[] args) { //注意这里，放入List的时候进行了什么操作？
        List shapes = Arrays.asList(new Circle(), new Square(), new Triangle());
        for(Shape shape : shapes) {//拿出来的时候又进行了什么操作？
            shape.draw();
        }
    }
}/*
 output: Circle.draw() Square.draw() Trangle.draw()

{% endhighlight java %}

- 编译时：这个例子中，Shape中的方法被申明为abstract,以此强制继承者覆写该方法。当把Shape对象放入List<Shape>中时会向上转型，但在向上转型为Shape的时丢失了Shape对象的具体类型，对于数组而言，他们只是Shape对象。当从list（所有的事物都当做Object的持有）中取出时，结果会转型为Shape（这是RTTI的最基本形式）。其实这是编译时由容器和java泛型强制这样做的，因此这样的转型是不彻底的。
- 运行时：由多态机制，Shape实际执行什么样的代码，是由引用所指向的具体对象Circle、Square、Triangle所决定的。

##### 2. Class对象
* 每个类都有一个Class对象，要理解RTTI在java中的工作原理，首先必须知道类型信息在运行时是怎么表示的，这项工作是由Class对象的特殊对象完成的，它包含了类有关的信息。实际上，Class对象是用来创建所有“常规”对象的。
* Class的加载：当Java创建某个类的对象，Java会检查内存中是否有相应的Class对象。如果内存中没有相应的Class对象，那么Java会在.class文件中寻找xxx类的定义，并加载xxx类的Class对象。在Class对象加载成功后，其他xxx对象的创建和相关操作都将参照该Class对象。
* 创建Class对象的两种方式：
	* Class class = forName("xxx");//方法接收一个字符串作为参数，该字符串是类的名字。这将返回相应的Class类对象。
	* Class class = xxx.class方法是直接调用类的class成员。这将返回相应的Class类对象。
* Class对象创建Object对象 Object obj = xxx.newInstance();
* 创建Class对象注意问题：有一个很有趣的现象，当使用.class来创建对Class对象的引用时，不会自动地初始化该Class对象。而Class.forClass("xx")则会立即初始化该Class对象。举一个例子吧：

{% highlight java linenos %}
package Chapter14;

import java.util.*;

/**
 * 
 * @author niushuai
 *
 * 这段程序说明的东西太多了。。。。。每个输出的顺序都是理解这个问题的的关键：
 * 
 * 1. 动态加载
 * 2. Lazy机制（还记得线段树的Lazy操作吗- -）
 * 3. 初始化和加载的关系
 * 4. static final数据是编译时候确定的，所以不需要初始化就可以使用。所以Initable.staticFinal在没有初始化的情况下就可以访问
 * 5. static final修饰的还可能在运行时才能确定 Initable.staticFinal2为例子
 * 6. static 非final的比如Initable.staticNoNFinal是先进行初始化才输出的
 */

class Initable {
	static final int staticFinal = 47;
	static final int staticFinal2 = ClassInitialization.rand.nextInt(1000);
	static {
		System.out.println("Initializing Initable");
	}
}

class Initable2 {
	static int staticNonFinal = 147;
	static {
		System.out.println("Initializing Initable2");
	}
}

class Initable3 {
	static int staticNonFinal = 74;
	static {
		System.out.println("Initializing Initable3");
	}
}

public class ClassInitialization {
	public static Random rand = new Random(47);
	public static void main(String[] args) {
		//发现没有初始化，因为没有用到！
		Class initable = Initable.class;
		System.out.println("After creating Initable ref");
		//staticFinal因为是编译期确定，如果只访问这个，也不会触发类的初始化。可以注释下面那个运行期确定的变量，则不会初始化
		System.out.println(Initable.staticFinal);
		//下面调用非编译期确定的static数据，Initable才被初始化
		System.out.println(Initable.staticFinal2);
		
		//调用Initable2的static数据，被加载并且初始化
		System.out.println(Initable2.staticNonFinal);
		try {
			//forName立即加载，不管使用不使用
			Class initable3 = Class.forName("Chapter14.Initable3");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		System.out.println("After creating Initable3 ref");
		System.out.println(Initable3.staticNonFinal);
	}
}/*output:
After creating Initable ref
47
Initializing Initable
258
Initializing Initable2
147
Initializing Initable3
After creating Initable3 ref
74
*/
{% endhighlight java %}

##### 3. 泛型化的Class引用

### 二、 反射

> 待续,明天写