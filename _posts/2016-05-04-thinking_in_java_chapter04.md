---
layout: post
title: "Java编程思想 - 第四章、控制执行流程"
date: 2016-05-04 14:14
categories: [java]
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