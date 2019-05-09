---
layout: post
title:  "completable-future-p1"
date:   2019-05-09 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


#### completable-future part1   

### 技术版本
jdk: 1.8    


### 前言
**网上帖子千千万，咱也一起炒冷饭.**  
CompletableFuture是用于构建异步编程的基础，同时它继承自Future和CompletionStage， CompletionStage这个接口是一个promise，它表示这个计算最终会完成。       

### Future的不足 
1.不能手动设置操作完成  
e.g. 开单独的线程通过rpc调一个接口返回一些数据,假如下游接口挂了，那么想通过返回上次缓存住的数据来进行手动完成，这时候Future没有这样的api支持   
2.只能阻塞式的操作Future的返回结果   
e.g 意思就是当结果可用的时候，Future不会主动通知你，你只能通过调用get()方法阻塞的等待返回结果。你没办法给Future设置一个回调，当结构可用的时候自动调用这个回调。   
3.多个Future没办法进行链式调用  
e.g 比如有2个耗时的计算（A,B），并且他们有依赖关系，当A计算完成，需要传递给B进行继续计算。Future不支持异步的流式计算调用。   
4.不支持组合多个Future  
e.g 比如有5个并发支持的Future，我们想在他们都执行完成后在做一些额外操作，Future不支持。
5. 异常处理  
Future api没有提供异常处理的机制   

### 案例开始 
#### 最简单的异步
```java
@Test
public void test1() throws ExecutionException, InterruptedException {
    CompletableFuture<String> completableFuture = new CompletableFuture<>(); //step1
    new Thread(new CompleteFutureTask(completableFuture)).start(); //step2
    String result = completableFuture.get(); //step3
    System.out.println(Thread.currentThread().getName() + " get result:" + result);
}

class CompleteFutureTask implements Runnable {
    CompletableFuture<String> completableFuture;

    public CompleteFutureTask(CompletableFuture<String> completableFuture) {
        this.completableFuture = completableFuture;
    }

    @Override
    public void run() {
        MixAll.simulateComputeCost();
        System.out.println(Thread.currentThread().getName() + " will complete future:" + completableFuture);
        completableFuture.complete("hello completableFuture"); //step4
        //completableFuture.complete("hello completableFuture"); //step5
    }
}
```
step1 最简单的方式创建一个CompletableFuture, 如果没有step2, 那么位于step3的get()方法会一直阻塞, 因为计算一直没有完成.     
同时step4 的多次调用是无效的, 即step5是无效的    

#### 通过runAsync()进行异步计算, 改方法返回CompletableFuture<Void>   
示例代码
```java
@Test
public void runAsyncTest() throws ExecutionException, InterruptedException {
    CompletableFuture<Void> cf = CompletableFuture.runAsync(() -> {
        MixAll.simulateComputeCost();
        System.out.println(Thread.currentThread().getName() + " runAsyncTest...................");
    });
    cf.get(); //step1
    //MixAll.simulateComputeCost(8); //step2
}
``` 
 由于这个是在junit里面进行的，所以需要等待它执行完成，等待的方式有2种: step1和step2. 
 **不要误认为必须调用get()方法**
该方法有2个重载方法   
```java
public static CompletableFuture<Void> runAsync(Runnable runnable)  
public static CompletableFuture<Void> runAsync(Runnable runnable,Executor executor)  
```
方法1使用的是ForkJoinPool的线程池, 方法2可以传入自定义的线程池   

#### supplyAsync()进行异步计算, 有返回结果   
示例代码
```java
@Test
public void supplyAsyncTest() throws ExecutionException, InterruptedException {
    CompletableFuture<String> cf = CompletableFuture.supplyAsync(() -> {
        MixAll.simulateComputeCost();
        MixAll.printWithThread(" will return something");
        return "hello supplyAsync";
    });

    String data = cf.get();
    MixAll.printWithThread(" get data:" + data);
}
```
依然有2个重载方法,详见api, 线程池说明同上   

#### 构建异步CompletableFuture
上面的get()是阻塞的, 他会一直阻塞直到执行完成,这就是最开始我们说的Future的不足,这不是我们想要的.  
我们需要的是可以设置一个回调,等执行完成后自动进行调用, 下面我们看下这几个方法.  
1.thenApply()方法,**它接受一个Function作为入参,同时返回带参的CompletableFuture** 
示例代码
```java
@Test
public void thenApplyTest() throws ExecutionException, InterruptedException {
    CompletableFuture<String> cf = CompletableFuture
            .supplyAsync(() -> {
                MixAll.simulateComputeCost();
                MixAll.printWithThread(" will return something");
                return "hello supplyAsync";
            })
            .thenApply(info -> {
                MixAll.simulateComputeCost();
                MixAll.printWithThread(" will return something");
                return "thenApply|" + info;
            });


    String data = cf.get();
    MixAll.printWithThread(" get>>" + data);
}
```
supplyAsync执行完成后,自动调用thenApply()方法  
执行thenApply里面的逻辑的线程和执行supplyAsync逻辑的是同一个.   

2.thenAccept(),返回CompletableFuture<Void>, 参数是Consumer   
示例代码,先异步获取数据,完成后进行消费      
```java
@Test
public void thenAcceptTest() {
    CompletableFuture.supplyAsync(() -> UserService.Instance.getUser(12))
            .thenAccept(user -> MixAll.printWithThread(" thenAccept get userName:" + user.getName()));

    //等待执行完成
    MixAll.simulateComputeCost(8);
}
```

3.thenRun(), 返回CompletableFuture<Void>, 参数是Runnable,无法访问上一步的返回结果(如果有返回结果)   
示例代码
```java
@Test
public void thenRunTest() {
    long start = System.currentTimeMillis();
    CompletableFuture
            .runAsync(() -> UserService.Instance.getUser(12))
            .thenRun(() -> MixAll.printWithThread(" thenRun cost:" + (System.currentTimeMillis() - start) + "ms"));
    //等待执行完成
    MixAll.simulateComputeCost(8);
}
``` 
这3个方法都有另外2个后缀是Async的重载方法，以thenApply为例  
```java
thenApplyAsync(Function<? super T,? extends U> fn)  //method1
thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)   //method2  
```
method1在方法内部使用了ForkJoinPool.commonPool(),在测试过程中发现执行thenApplyAsync的逻辑和执行supplyAsync的线程依然是同一个,猜测是又在线程池里拿到了同一个,但是好巧啊...   
method2使用了传递的executor进行执行   

