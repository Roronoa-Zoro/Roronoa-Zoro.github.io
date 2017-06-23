---
layout: post
title:  "java interrupt"
date:   2017-06-23 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}

### 前言
java关于interrupt笔记
  
jdk version: 1.8  

### 说明  
t.isInterrupted(),用来判断线程的中断状态  
t.interrupt(),用来中断该线程  
Thread.interrupted(),用来恢复线程的中断状态  

#### 举个例子(简单场景)
调用t.interrupt()可以中断线程t  
此时t.isInterrupted()为true  
调用Thread.interrupted(),操作返回true  
调用t.isInterrupted()检查中断状态,为false  
若再次调用Thread.interrupted(),此时返回false,即已经重置过了

### 更多内容详见api
[jdk1.8 api](http://docs.oracle.com/javase/8/docs/api/)