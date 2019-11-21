---
layout: post
title:  "sprin-refresh"
date:   2019-11-21 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


### 系统版本
jdk：1.8
spring：5.2.1

### spring refresh
AbstractApplicationContext    
```java
// Allows post-processing of the bean factory in context subclasses.
postProcessBeanFactory(beanFactory);

// Invoke factory processors registered as beans in the context. 
// 在这里把值加到了environment里面，同时构造PropertySourcePlaceholderConfigurer
invokeBeanFactoryPostProcessors(beanFactory);

// Register bean processors that intercept bean creation.
registerBeanPostProcessors(beanFactory);

// Initialize message source for this context.
initMessageSource();

// Initialize event multicaster for this context.
initApplicationEventMulticaster();

// Initialize other special beans in specific context subclasses.
onRefresh();

// Check for listener beans and register them.
// 在这里会调用populateBean方法进行处理属性值，多个PropertySourcePlaceholderConfigurer按优先级排序循环处理
registerListeners();

// Instantiate all remaining (non-lazy-init) singletons.
finishBeanFactoryInitialization(beanFactory);
```
