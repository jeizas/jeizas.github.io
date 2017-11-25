---
layout: post
title: "tomcat性能调优"
date: 2016-11-07 21:38
categories: [skill]
tags: tools
---

### 一、本章要点

> 本章主要记录记录下tomcat的性能调优过程，点滴积累，铸就辉煌。最近随着项目代码数量的逐渐“堆积”，后台处理业务的范围逐渐增大，但是程序的性能却不断的出现问题。从最开始tomcat经常出现OOM，到运行一天左右后服务器CPU暴增到100%，再到服务器运行半天后CPU维持在60%左右，运行一段时间网站响应延迟较高...

### 二、探索

* server -Xms1024m -Xmx1500m -XX:MaxNewSize=640m -XX:PermSize=128m -XX:MaxPermSize=256m -Djava.awt.headless=true -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/tomcat/logs/
* server -Xms1024m -Xmx1500m -XX:MaxNewSize=640m -XX:PermSize=128m -XX:MaxPermSize=256m -Djava.awt.headless=true -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/tomcat/logs/ -XX:-UseGCOverheadLimit -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:+UseParNewGC  -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly
* -server -Xms1024m -Xmx1500m -XX:MaxNewSize=640m -XX:PermSize=128m -XX:MaxPermSize=384m -Djava.awt.headless=true -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/tomcat/logs/ -XX:-UseGCOverheadLimit -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:ParallelGCThreads=2 -XX:MaxTenuringThreshold=5 -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:+UseFastAccessorMethods


> 待续。

### 友情链接
* [tomcat调优指南](http://www.jeizas.me)
