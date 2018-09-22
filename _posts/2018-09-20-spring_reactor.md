---
layout: post
title: "spring5 响应式编程"
date: 2018-09-20 03:10
categories: [skill]
tags: java
---

## Spring5 响应式编程

### 产生背景

随着现代硬件的处理能力飞速发展，好像通过怼硬件的方式也可以解决一定的性能问题，但是也会大大增加成本，作为开发人员如何提高硬件利用率，增加吞吐量，构建高性能的应用，应该是一直探索的问题。广义来说我们有两种思路来提升程序性能：

1. **并行化（parallelize）** ：通过更好的硬件资源使用更多的线程
2. **异步**：基于现有的资源来 **提高执行效率** 。

​        第一种思路：并行化。通常，Java开发者使用阻塞式（blocking）编写代码。这没有问题，在出现性能瓶颈后， 我们可以增加处理线程，但是线程中同样是阻塞的代码。这种使用资源的方式会迅速面临资源竞争和并发问题。具体来说，比如当一个程序面临延迟（通常是I/O方面， 比如数据库读写请求或网络调用），所在线程需要进入 idle 状态等待数据，从而浪费资源。总体来说，这是这种方式依赖硬件水平，增加了编码的复杂性，而且容易造成资源浪费。所以，并行化方式并不是一种好的手段。阻塞是对资源的浪费。

​        第二种思路：提高执行效率。可以解决资源浪费问题，通过编写*异步非阻塞* 的代码，（任务发起异步调用后）执行过程会切换到另一个 **使用同样底层资源** 的线程中执行任务，然后等异步调用处理再返回结果。但是在目前在 JVM 上如何编写异步代码呢？Java 提供了两种异步编程方式：

1. **回调（Callbacks）** ：异步方法没有返回值，而是采用一个 `callback` 作为参数（lambda 或匿名类），当结果出来后回调这个 `callback`。如下

   ```java
   public Object test(@PathVariable("id") String name) throws InterruptedException {
       getFriends(id, new Callback<Integer>() {
           @Override
           public void onSuccess(Integer x) {
               if (x == 10) {
                   getSuggestions(new Callback<Integer>() {
                       @Override
                       public void onSuccess(Integer integer) {
                           System.out.println("success getSuggestions");
                       }
                   });
               } else {
                   getDetail(new Callback<Integer>() {
                       @Override
                       public void onSuccess(Integer integer) {
                           System.out.println("success getDetail");
                       }
                   });
               }
           }
       });
       return null;
   }
   
   /**
    * 获取用户朋友
    **/
   private void getFriends(String id, Callback<Integer> callback) {
       new Thread(() -> {
           try {
               Thread.sleep(100);
               callback.onSuccess(100);
           } catch (InterruptedException e) {
               callback.onSuccess(null);
           }
       }).start();
   }
   public interface Callback<T> {
       void onSuccess(T t);
   }
   ```

   **地狱回调（callback hell）**

   ![](https://leanote.com/api/file/getImage?fileId=5a7bbc00ab6441766a000a18)

2. **Future** ：异步方法 **立即** 返回一个 `Future<T>`，该异步方法要返回结果的是 `T` 类型，通过`Future`封装。这个结果并不是 *立刻* 可以拿到，而是等实际处理结束才可用。比如， `ExecutorService` 执行 `Callable<T>` 任务时会返回 `Future` 对象。

3. **CompletableFuture**：也是在Java 8中新增的，相对于原来的`Future`，它有两方面的亮点

   - 异步回调：它提供了五十多种方法，其中以`Async`结尾的方法都可以异步的调用而不会导致阻塞

   - 声明式：调用join的时候等待返回结果。

     ```java
     completableFuture.thenApplyAsync(...).thenApplyAsync(...).thenAcceptAsync(...);
     ```

   举个例子：比如我们在咖啡店买咖啡，点餐之后我们首先会拿到一张小票，这个小票就是`Future`，代表你凭此票在咖啡做好之后就可以去拿了。但是`Future.get()`方法仍然是同步和阻塞的，意味着你拿着票可以去找朋友聊会天，但是并不知道自己的咖啡什么时候做好，或者是去柜台拿的时候还是要等一会儿，就和朋友不能聊天了。而提供`CompletableFuture`服务的咖啡厅，不仅有小票，还有一个号牌，我们点餐之后找个桌坐下就好，这个订单的咖啡一旦做好就会送到我们手中。

   相对于回调和`Future`来说，`CompletableFuture`的功能强大了不少，我们来尝试使用它来实现这样一个需求：我们首先得到 ID 的列表，然后对每一个ID进一步获取到“ID对应的name和statistics”这样一对属性的组合为元素的列表，整个过程用异步方式来实现。

   ```java
   CompletableFuture<List<String>> ids = CompletableFuture.supplyAsync(() -> Arrays.asList("A", "B", "C", "D", "E", "F"));
   
   CompletableFuture<List<String>> result = ids.thenComposeAsync(l -> {
       List<CompletableFuture<String>> combinationList = l.stream().map(i -> {
           CompletableFuture<String> nameTask = CompletableFuture.supplyAsync(() -> getName(i));
           CompletableFuture<Integer> statTask = CompletableFuture.supplyAsync(() -> getStats(i));
   
           return nameTask.thenCombineAsync(statTask, (name, stat) -> "Name " + name + " has stats " + stat);
       }).collect(Collectors.toList());
       CompletableFuture[] combinationArray = combinationList.toArray(new CompletableFuture[0]);
   
       CompletableFuture<Void> allDone = CompletableFuture.allOf(combinationArray);
       return allDone.thenApply(v -> combinationList.stream()
                                .map(CompletableFuture::join)
                                .collect(Collectors.toList()));
   });
   
   List<String> results = result.join();
   ```

   可以看到`CompletableFuture`也尽力了，虽然使出浑身解数，但对于集合的操作还略显吃力，下面我们说一种更为优雅的方式：响应式编程。

4. Reactor：响应式框架，内置许多组合操作，因此以上例子可以简单地实现为：

   ```java
   @RequestMapping(value = "/rx", method = RequestMethod.GET)
   public Object test() {
       Flux<String> ids = Flux.fromStream(Stream.of("A", "B", "C", "D", "E", "F"));
       Flux<String> combinations = ids.flatMap(id -> {
               Mono<String> nameTask = Mono.just(id)
                   .flatMap(i -> Mono.just(getName(i)));
               Mono<Integer> statTask = Mono.just(id)
                   .flatMap(i -> Mono.just(getStats(i)));
               return nameTask.zipWith(statTask).map(Tuple2::toString);
           });
       // List<String> results = combinations.collectList().block();
       return combinations;
   }
   
   ```

   这种非阻塞数据流的感觉，让我想起来了《让子弹飞》里边最经典的一段：姜文饰演的张麻子朝新来县长那“马拉的火车啪啪啪连续打了N枪，旁边兄弟问“打中没有”，张麻子说“让×××飞一会儿~”，稍后就见拉火车的马缰绳全都被×××打断，马匹四散，非常6+1！如果张麻子每打一枪都看看前一枪有没有射中的话，还怎么装X呢？

### 响应式编程介绍

提到响应式，前端的童鞋们可能会想到“响应式设计”，是一种网页布局和样式能够自适应不同大小尺寸的屏幕。而今天我们提到的响应式编程（reactive programming）是一种面向数据流的编程思想或者说编程框架。

> 在计算机中，**响应式编程**或**反应式编程**（英语：Reactive programming）是一种面向数据流和变化传播的编程范式

- Data streams：即数据流，分为静态数据流（比如数组，文件）和动态数据流（比如事件流，日志流）两种。基于数据流模型，RP 提供一套统一的 Stream 风格的数据处理接口。和 Java 8 中的 Stream API 相比，RP API 除了支持静态数据流，还支持动态数据流，并且允许复用和同时接入多个订阅者。
- The propagation of change：即变化传播，简单来说就是以一个数据流为输入，经过一连串操作转化为另一个数据流，然后分发给各个订阅者的过程。这就有点像函数式编程中的组合函数，将多个函数串联起来，把一组输入数据转化为格式迥异的输出数据，如stream().map.filter....

#### **前十今生**

在响应式编程方面，微软跨出了第一步，它在 .NET 生态中创建了响应式扩展库（Reactive Extensions library, Rx）。接着**RxJava**在JVM上实现了响应式编程。Java社区的一些大牛凑到一起制定了一个[响应式流规范](https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.2/README.md)。RxJava团队随后对1版本进行了重构，形成了兼容该响应流规范的RxJava 2。在 `Java` 平台上，`Netflix`（开发了 `RxJava`）、`TypeSafe`（开发了 `Scala`、`Akka`）、`Pivatol`（开发了 `Spring`、`Reactor`）共同制定了一个被称为 `Reactive Streams` 项目（规范），用于制定反应式编程相关的规范以及接口，在Java 9版本中，响应式流的规范被纳入到了JDK中。

**Reactor**是Pivotal旗下的项目，与大名鼎鼎的Spring是兄弟关系，因此是Spring近期推出的响应式模块WebFlux的“御用”响应式流。Reactor支持响应式流规范，与RxJava相比，它没有任何历史包袱，专注于Server端的响应式开发，而RxJava更多倾向于Android端的响应式开发。

#### 模式

由于WebFlux首选Reactor作为其响应式技术栈的一部分，我们下边也主要以Reactor为主。响应式编程通常作为面向对象编程中的“观察者模式”（Observer design pattern）的一种扩展。 响应式流（reactive streams）与“迭代子模式”（Iterator design pattern）也有相通之处， 因为其中也有 Iterable-Iterator 这样的对应关系。主要的区别在于，Iterator 是基于 “拉取”（pull）方式的，而响应式流是基于“推送”（push）方式的。

使用 iterator 是一种“命令式”（imperative）编程范式，因为什么时候获取下一个元素取决于开发者。在响应式流中，相对应的角色是“发布者 - 订阅者”（Publisher-Subscriber），当有新的值到来的时候，反过来由发布者（Publisher） 通知订阅者（Subscriber），这种“推送”模式是响应式的关键。此外，对推送来的数据的操作 是通过一种声明式（declaratively）而不是命令式（imperatively）的方式表达的：开发者通过 描述“处理流程”来定义对数据流的处理逻辑。

![](https://leanote.com/api/file/getImage?fileId=5a7bf5f3ab6441785e0014ee)

#### 特点

- **可编排性（Composability）** 以及 **可读性（Readability）**

- 使用丰富的 **操作符** 来处理形如 **流** 的数据

- 在 **订阅（subscribe）** 之前什么都不会发生

- **背压（backpressure）** 具体来说即 *消费者能够反向告知生产者生产内容的速度的能力*

  ```java
  public static enum OverflowStrategy {
      IGNORE,
      ERROR,
      DROP,
      LATEST,
      BUFFER;
  
      private OverflowStrategy() {
      }
  }
  ```

  ![buffer](https://raw.githubusercontent.com/reactor/reactor-core/v3.1.3.RELEASE/src/docs/marble/onbackpressurebuffer.png)

- **高层次** （同时也是有高价值的）的抽象，从而达到 *并发无关* 的效果

缺点

- 调试需要学习成本，官方提供了方法

### Reactor 语法介绍

![](https://upload-images.jianshu.io/upload_images/4366140-0478dfc952615288.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如同 `Java 8` 所引入的 `Stream` 一样，`Reactor` 的使用方式基本上也是分三步：

##### 开始阶段的创建

Reactor 引入了实现 `Publisher` 的响应式类 `Flux` 和 `Mono`，以及丰富的操作方式。 一个 `Flux` 对象代表一个包含 0..N 个元素的响应式序列，而一个 `Mono` 对象代表一个包含 0/1元素的响应式编程。

```java
Mono<String> noData = Mono.empty(); 
Mono<String> data = Mono.just("foo");

Flux<String> seq1 = Flux.just("foo", "bar", "foobar");
List<String> iterable = Arrays.asList("foo", "bar", "foobar");
Flux<String> seq2 = Flux.fromIterable(iterable);
```



##### 中间阶段的处理

中间阶段的 `Mono` 和 `Flux` 的方法主要有 `filter`、`map`、`flatMap`、`then`、`zip`、`reduce` 等。这些方法使用方法和 `Stream` 中的方法类似。

```java
// 传统
Object result1 = doStep1(params);
Object result2 = doStep2(result1);
Object result3 = doStep3(result2);

// reactor
Mono.just(params)
    .flatMap(v -> doStep1(v))
    .flatMap(v -> doStep2(v))
    .flatMap(v -> doStep3(v));
```



##### 最终阶段的消费

直接消费的 `Mono` 或 `Flux` 的方式就是调用 `subscribe()` 方法。如果在 `WebFlux` 接口中开发，直接返回 `Mono` 或 Flux 即可。`WebFlux` 框架会完成最后的 `Response` 输出工作。

```java
subscribe(); 

subscribe(Consumer<? super T> consumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer,
          Consumer<? super Subscription> subscriptionConsumer);
```



### webflux介绍

Spring WebFlux是Spring Framework 5.0中引入的新的反应式Web框架。 由于响应式编程的特性，Spring WebFlux和Reactor底层需要支持异步的运行环境，比如Netty和Undertow；也可以运行在支持异步I/O的Servlet 3.1的容器之上，比如Tomcat（8.0.23及以上）和Jetty（9.0.4及以上)

WebFlux 所使用的类型是与反应式编程相关的 Flux 和 Mono 等，而不是简单的对象。对于简单的 Hello World 示例来说，这两者之间并没有什么太大的差别。对于复杂的应用来说，反应式编程和负压的优势会体现出来，可以带来整体的性能的提升

```java
@RestController
public class BasicController {
    @GetMapping("/hello_world")
    public Mono<String> sayHelloWorld() {
        return Mono.just("Hello World");
    }
}
```

但是 PersonRepository 这种 Spring Data Reactive Repositories 不支持 MySQL，进一步也不支持 MySQL 事务。所以用了 Reactivey 原来的 spring 事务管理就不好用了。jdbc jpa 的事务是基于阻塞 IO 模型的，如果 Spring Data Reactive 没有升级 IO 模型去支持 JDBC，生产上的应用只能使用不强依赖事务的。也可以使用透明的事务管理，即每次操作的时候以回调形式去传递数据库连接 connection。

Spring Data Reactive Repositories 目前支持 Mongo、Cassandra、Redis、Couchbase 。

![](https://leanote.com/api/file/getImage?fileId=5a929813ab644121e3000f8c)

从图的纵向上看，spring-webflux上层支持两种开发模式：

- 类似于Spring WebMVC的基于注解（`@Controller`、`@RequestMapping`）的开发模式

- ```JAVA
  @RestController
  @RequestMapping("/api/user")
  public class WebFluxController {
  
      private Map<Long,User> map = new HashMap<Long,User>(10);
      @PostConstruct
      public void init(){
          map.put(1L,new User(1,"admin","admin"));
          map.put(2L,new User(1,"admin2","admin2"));
          map.put(3L,new User(1,"admin3","admin3"));
      }
      @GetMapping("/getAll")
      public Flux<User> getAllUser(){
          return Flux.fromIterable(map.entrySet().stream().map(Map.Entry::getValue)
                  .collect(Collectors.toList()));
      }
      @GetMapping("/{id}")
      public Mono<User> getUserById(@PathVariable("id") Long id){
          return Mono.just(map.get(id));
      }
      @PostMapping("/save")
      public Mono<ResponseEntity<String>> save(@RequestBody User user){
          map.put(user.getUid(),user);
          return Mono.just(new ResponseEntity<>("添加成功", HttpStatus.CREATED));
      }
  }
  ```


- 基于Java8 lambda样式路由和处理

  ```JAVA
  // 业务处理类
  @Component
  public class UserHandler {
  
      private IUserService userService;
      @Autowired
      public UserHandler(IUserService userService) {
          this.userService = userService;
      }
  
      public Mono<ServerResponse> getAllUser(ServerRequest serverRequest){
          Flux<User> allUser = userService.getAllUser();
          return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(allUser,User.class);
      }
  
      public Mono<ServerResponse> getUserById(ServerRequest serverRequest){
          //获取url上的id
          Long uid = Long.valueOf(serverRequest.pathVariable("id"));
          Mono<User> user = userService.getUserById(uid);
          return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(user,User.class);
      }
  
      public Mono<ServerResponse> saveUser(ServerRequest serverRequest){
          Mono<User> user = serverRequest.bodyToMono(User.class);
          return ServerResponse.ok().build(userService.saveUser(user));
      }
  }
  
  // 为Handler类添加路由信息
  @Configuration
  public class RoutingConfiguration {
  
      @Bean
      public RouterFunction<ServerResponse> monoRouterFunction(UserHandler userHandler){
          return route(GET("/api/user").and(accept(MediaType.APPLICATION_JSON)),userHandler::getAllUser)
                  .andRoute(GET("/api/user/{id}").and(accept(MediaType.APPLICATION_JSON)),userHandler::getUserById)
                  .andRoute(POST("/api/save").and(accept(MediaType.APPLICATION_JSON)),userHandler::saveUser);
      }
  }
  ```

对于上文提到， `Subscriber` 和 `Subcription` 这两个接口，`Reactor` 也有相应的实现。这些都是 `Spring WebFlux` 和 `Spring Data Reactive` 这样的框架用到的。如果 **不开发中间件**，开发人员是不会接触到的。

#### 参考资料

- [Spring Reactor 入门与实践](https://www.jianshu.com/p/7ee89f70dfe5)
- [使用 Reactor 进行反应式编程](https://www.ibm.com/developerworks/cn/java/j-cn-with-reactor-response-encode/index.html)
- [Java 平台反应式编程（Reactive Programming）入门](https://cloud.tencent.com/developer/article/1073888)
- [漫谈响应式编程](http://codebay.cn/post/2811.html)
- [响应式编程（上）：总览](https://juejin.im/entry/5a0ac2215188252abc5de24f)
- [Reactor 指南中文版V2.0](http://projectreactor.mydoc.io/?t=45203)
- [中文翻译文档地址](http://htmlpreview.github.io/?https://github.com/get-set/reactor-core/blob/master-zh/src/docs/index.html)
- [响应式流——响应式Spring的道法术器](http://blog.51cto.com/liukang/2090183)

