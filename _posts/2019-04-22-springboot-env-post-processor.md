---
layout: post
title:  "springboot-env-post-processor"
date:   2019-04-22 +0800
categories: JAVA
tags: springboot
author: Jimmy Lee
---

* content
{:toc}


#### springboot-env-post-processor   


### 技术版本
springboot: 2.0.3     
java: 1.8    


### 说明
1.加载properties或者yml配置文件的类ConfigFileApplicationListener    
2.org.springframework.core.Ordered 接口的顺序是值越小, 顺序越提前     

### 案例说明  
1.基于rocketmq做了一个刷新缓存的工具,为了保证分组相同,第一版本取的hostname作为consumer group
2.部署后发现机器的hostname带 ".",这是运维的规范  
3.改进, 继承ConfigFileApplicationListener, 顺序比它大, 逻辑是取spring.cloud.client.hostname,然后把"."替换成"-",然后使用一个新key放入env,然后consumer group使用新key
4.详见cache-refresh项目  

