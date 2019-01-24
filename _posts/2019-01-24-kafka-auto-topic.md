---
layout: post
title:  "kafka-auto-topic"
date:   2019-01-24 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


#### kafka动态配置topic  


### 技术版本
spring boot: 2.0.3.RELEASE  
spring-kafka: 2.1.4.RELEASE  
jdk: 1.8  


配置文件示例代码
```yml
spring: 
  kafka: 
    topics: xxx
```

java监听器示例代码
```java
@KafkaListener(topics = "#{'${spring.kafka.topics}'.split(',')}")
    public void receive(@Payload List<String> message) {
        message.stream().filter(m -> MessageConverter.Instance.convert(m) != null)
                .map((String s) -> MessageConverter.Instance.convert(s))
                .forEach(m -> {
                    //business logic

                });
    }
```
