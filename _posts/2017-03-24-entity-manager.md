---
layout: post
title:  "entity manager close"
date:   2017-03-24 +0800
categories: ORM
tags: jpa
author: Jimmy Lee
---

* content
{:toc}

### 前言
entity manager未关闭造成的影响

spring jpa version:1.10.1  
底层hibernate: 5.1.0 

### 场景
某些情况需要自行写sql,然后就用到了EntityManager, 用完之后没有显示的close(==|||)

上线后大量请求超时,偶尔有那么几个请求会快


### 问题定位
首先确定不是数据库正在的查询慢,目前量还很小,然后找到sql去数据库查询系统确认,查询速度接近0ms

然后怀疑是网络延迟,然后找到接收到前端传递过来的参数,想让前端查查log他们发送的时间点,没人搭理...无果

找运维同学要了份error log,一看,果然不是网络问题,大量报获取不到数据库链接,等待获取连接超时,当时心一惊

想着用的spring jpa,底层使用的数据库链接应该会自动释放啊,找dba看了下应用系统连到数据库的活跃连接数,果然每个应用的连接数都到了最大值

想了想,有地方用了EntityManager,找到使用的地方一看,没有手动关闭,哎....

加上close后,问题解决...

### 后记
没有任何技术含量,给自己的一个提醒,用惯了自动释放连接,以后得多注意