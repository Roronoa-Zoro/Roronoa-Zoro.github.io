---
layout: post
title:  "filter-and-interceptor"
date:   2017-11-08 +0800
categories: JAVA
tags: spring
author: Jimmy Lee
---

* content
{:toc}


### 前言  
filter和拦截器的执行顺序  


### 顺序
1. filter先于拦截器, 而且filter的执行顺序取决于声明的顺序


### 调用顺序  
request --> filter1 --> filter2 --> interceptor1 --> interceptor2 --> dispatcherServlet 
--> controller -- modelAndView --> dispatcherServlet --> 视图渲染 --> interceptor2(afterCompletion)  
--> interceptor1(afterCompletion) --> filter2 --> filter1
