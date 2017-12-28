---
layout: post
title:  "synchronize state"
date:   2017-12-28 +0800
categories: JAVA
tags: concurrent
author: Jimmy Lee
---

* content
{:toc}


### 代码 
```java
Object obj = new Object();

public void sync() {
    synchronized(obj) {
        try {
            TimeUnit.SECONDS.sleep(Integer.MAX_VALUE);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    System.out.println("method sync quit");
}
``` 


### 问题
如何唤醒调用的该方法的线程,让方法继续往下执行


### 问答    
1. 本人只知道可以调用线程的interrupt方法, 让线程响应中断.
2. 对方很笃定的说可以调用obj.notifyAll()来唤醒,然后说了一堆,说实话让我很懵逼.  
让我不解的地方主要集中在第一个线程都没有调用wait方法退出监视器,进入到等待区,怎么能调用notify进行唤醒呢?  


### 证实
```java
public void notifySync() {
    obj.notifyAll();
    System.out.println("notifySync invoked");
}
```
实验一下, 第二个线程调用上面的方法, 果然抛了异常:java.lang.IllegalMonitorStateException.  


来看一下api对这个异常的说明.  


Thrown to indicate that a thread has attempted to wait on an object's monitor or to notify other threads waiting on an object's monitor without owning the specified monitor.  


也就是说调用wait/notify/notifyAll方法, 那么执行相应方法的线程必须持有监视器才行.  


### 结论
1. 对方的观点错误
2. 自己对基础的掌握不够牢固, 特此记录一下.
