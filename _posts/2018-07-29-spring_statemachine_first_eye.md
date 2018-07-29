---
layout: post
title: "spring-statemachine 初探"
date: 2018-07-29 23:35
categories: [life]
tags: life
---

### spring-statemachine 初探

##### 一句话介绍
> 将有限个不变的状态以及在这些状态之间的转移和动作行为实现事件驱动迁移目标状态的的一个框架

#### 使用场合

> 应用程序的状态数量是固定的，并且事件会按照顺序依次在不同状态之间转换

- 电商场景（订单、物流、售后）、社交（IM消息投递）、分布式集群管理（分布式计算平台任务编排）等场景都有大规模的使用

#### 优点

- 配置状态以及转移需要的事件，采用层次化状态机结构简化复杂状态配置，比原有实现方式更加解耦合，逻辑更加清晰，方便维护
- 可定义状态转移需要执行的action
- 基于ZooKeeper实现的分布式状态机 
- 内置redis存储
- 有事件监听器 

#### 缺点
- 单例statemachine状态机实例较重，状态转移依赖statemachine，不支持并发
- 工厂模式会中statemachine会存储context


#### 实现方式

* 单例
> 只有一个statemachine实例，重置状态机(设置当前状态)并处理statemachine.send(event)，实例不能够被多线程共享。

	- StateMachineListener 跟踪状态转移
	- StateChangeInterceptor 改变状态转移链的变化
	
{% highlight java linenos %}
stateMachine.stop();
        stateMachine.getStateMachineAccessor()
                .doWithAllRegions(access -> access.resetStateMachine(
                        new DefaultStateMachineContext<States, Events> (sourceState, null, null, extendedState)));
        stateMachine.start ();
        boolean sendEvent = stateMachine.sendEvent(event);
{% endhighlight %}

* 工厂

	- 获取stateMachine
	
{% highlight java linenos %}
public StateMachine<S, E> create(String machineId) {
        StateMachine<S, E> stateMachine = stateMachineFactory.getStateMachine(machineId);
        stateMachine.start();
        return stateMachine;
    }
{% endhighlight %}
	
	- 事件监听目前还未demo
	
### 资料
- [状态机引擎选型](https://segmentfault.com/a/1190000009906317)
- [状态机选型简记](http://childe.net.cn/2018/04/28/%E7%8A%B6%E6%80%81%E6%9C%BA%E9%80%89%E5%9E%8B%E7%AE%80%E8%AE%B0/)
- [基于注解的实现](https://www.codetd.com/article/1010726)
- [理解状态机](https://glumes.com/post/android/understand-state-machine/)