---
layout: post
title:  "spring-boot-config"
date:   2018-07-24 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


#### springboot配置汇总  


### 技术版本
spring boot: 2.0.3.RELEASE    
jdk: 1.8   


1.分环境配置日志   
在不同的配置文件定义日志级别,如logging.level.root=debug/info   
定义logback-spring.xml文件   
```xml
<springProperty scope="context" name="logLevel" source="logging.level.root"/>
```   
然后可以引用${logLevel}     

2.yml里面配置list，使用@Value取值
```yml
mm: 
  list: aaa,bbb,ccc
```
```java
@Value("#{'${mm.list}'.split(',')}")
private List<String> lists;
```

3.yml里面配置map，使用@Value取值
```yml
mm: 
  map: "{'aaa':1, 'bbb':2, 'ccc': 3}"
```
```java
@Value(#{${"mm.map"}})
private Map<String, Integer> maps;
```
