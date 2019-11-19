---
layout: post
title:  "checkstyle-config"
date:   2019-11-19 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


### checkstyle maven plus 配置
#### 指定checkstyle配置的位置  
参考:[apache maven checkstyle](https://maven.apache.org/plugins/maven-checkstyle-plugin/examples/custom-checker-config.html)     
```xml
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-checkstyle-plugin</artifactId>
        <version>3.1.0</version>
        <configuration>
          <configLocation>config/checkstyle/checkstyle.xml</configLocation>
        </configuration>
      </plugin>
```
