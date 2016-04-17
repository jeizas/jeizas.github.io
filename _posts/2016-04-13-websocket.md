---
layout: post
title: "websocket学习笔记"
date: 2016-04-13 23:20
categories: [markdown]
tags: [java]
---
### 本章要点

> 本章主要介绍服务器与浏览器之间实时通讯的几种方式:ajax 论询、long poll、DWR框架、websocket。重点说明在java web的世界中websocket整合spring框架的配置，初步搭建一个类似聊天室的系哦。

### 1. ajax论询

* 原理：让浏览器隔个几秒就发送一次请求，询问服务器是否有新信息
* 实例：

{% highlight java linenos %}
	function getDate(){
    	$.post("/url", {data:data}, function (json) {
        	//数据处理
        }
    }
	$(function () {//间隔3s自动加载一次  
		getData(); //首次立即加载  
		window.setInterval(getData, 3000); //循环执行！！  
	}); 
{% endhighlight java %}

### 2. Long poll 长连接

- 原理：其实原理跟 ajax轮询 差不多，都是采用轮询的方式，不过采取的是阻塞模型（一直打电话，没收到就不挂电话），也就是说，客户端发起连接后，如果没消息，就一直不返回Response给客户端。直到有消息才返回，返回完之后，客户端再次建立连接，周而复始。

### 3. DWR框架

- 原理：DWR的工作原理是通过动态把Java类生成为Javascript。它不需要任何网页浏览器插件就能运行在网页上。

### 4. websocket

- 原理：Websocket是基于HTTP协议的
- **未完待续**