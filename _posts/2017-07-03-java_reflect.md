---
layout: post
title: "java 反射"
date: 2017-07-03 23:45
categories: [java]
tags: java
---

### 一、简介

> 在Java运行时环境中，对于任意一个类，可以知道这个类有哪些属性和方法。对于任意一个对象，可以调用它的任意一个方法。这种动态获取类的信息以及动态调用对象的方法的功能来自于Java语言的反射（Reflection）机制。
> Java的反射机制的实现要借助于4个类：Class，Constructor，Field，Method。

### 二、反射的功能

- 在运行时判断任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判断任意一个类所具有的成员变量和方法
- 在运行时调用任意一个对象的方法

### 三、反射相关类

##### 1.Class

> 

##### 2.Constructor

> 类的构造器对象

```java
// -- 获得使用特殊的参数类型的公共构造函数
Constructor getConstructor(Class[] params)
// -- 获得类的所有公共构造函数 
Constructor[] getConstructors()
// -- 获得使用特定参数类型的构造函数(与接入级别无关) 
Constructor getDeclaredConstructor(Class[] params) 
// -- 获得类的所有构造函数(与接入级别无关)
Constructor[] getDeclaredConstructors()
```

##### 3.Field

> 类的属性对象

```java
// -- 获得命名的公共字段 
Field getField(String name)
// -- 获得类的所有公共字段  
Field[] getFields()
// -- 获得类声明的命名的字段 
Field getDeclaredField(String name)
// -- 获得类声明的所有字段
Field[] getDeclaredFields()
```

##### 4.Method

> 获取类的方法信息

```java
// -- 使用特定的参数类型，获得命名的公共方法 
Method getMethod(String name, Class[] params)
// -- 获得类的所有公共方法 
Method[] getMethods()
// -- 使用特写的参数类型，获得类声明的命名的方法 
Method getDeclaredMethod(String name, Class[] params)
// -- 获得类声明的所有方法
Method[] getDeclaredMethods()
```
