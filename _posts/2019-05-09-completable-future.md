---
layout: post
title:  "completable-future"
date:   2019-05-09 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


#### completable-future  

### 技术版本
jdk: 1.8    


### 前言
**网上帖子千千万，咱也一起炒冷饭.**  
CompletableFuture是用于构建异步编程的基础，同时它集成自Future和CompletionStage， 这个接口是一个promise，它表示这个计算最终会完成。       

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
    cf.get();
}
```
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
