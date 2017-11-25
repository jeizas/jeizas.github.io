---
layout: post
title: "Java编程思想 - 第四章、控制执行流程"
date: 2016-05-04 14:14
categories: [skill]
tags: [Java编程思想]
---

### 本章要点

> 本章重点掌握if-else、do-while、for、Foreach、break、continue、以及goto、label的用法

#### 一、 常见的控制语句

* java 使用了c所有的流程控制语句，java并不支持goto语句（该语句引起许多反对意见，但它仍然是解决某些特殊问题的最便利的方法），在java中仍可以使用类似goto一样的跳转，但比起经典的goto，有了很多的限制。

#### 二、 逗号表达式

* java中唯一使用逗号操作符的地方就是for循环的控制表达式，在表达式初始化和步进控制部分

~~~java
for(int i = 0, j = i +1; i < 10; i++)
~~~

#### 三、 臭名昭著的goto

* 起源：自从Edsger Dijkstra发表了著名的论文《goto considered harmful》（goto有害论），众人开始痛斥goto的不是。其实在汇编的中我们就接触过goto，它是在源码级上的跳转，若一个程序总是从一个地方跳到另一个地方，这样程序可读性就会很差，维护成本就会增大。
* java中：在java中goto仍然是保留字，但是在语言中并不使用它。
* 替代：用continue+label替代goto的作用。在JAVA中，标签起作用的唯一地方刚好是在迭代语句前面。“刚好之前”的意思表明，在标签和迭代之间置入任何语句都不行。而在迭代之前设置标签的唯一理由是：我们希望在其中嵌套另一个迭代或者一个开关。因为break和continue通常只是中断当前循环，但若随同标签一起使用，它们就会中断循环，直接到达标签所在的地方
* 规则：
   * 一般的continue会退回最内层循环的开头，并继续执行
   * 带标签的continue会到达标签的位置，并重新进入紧接在那个标签后面的循环
   * 一般的break会中断并跳出当前循环
   * 带标签的break会中断并跳出标签所指的循环，也就是跳过这个标签后的整个循环

{% highlight java linenos %}
label1:
outer-interation{
	innner-inteation{
    	braak;
        //终止内部循环，会到外部循环迭代
        continue;
        //执行点会到内部迭代的起始处
        continue label1;
        //同时终止内外迭代，直接跳转到label1处，继续执行，迭代从外部开始
        break label1;
        //中断label1紧跟的所有中断，跳转标签处，但是不会重新进入迭代
    }
}
{% endhighlight java linenos %}
