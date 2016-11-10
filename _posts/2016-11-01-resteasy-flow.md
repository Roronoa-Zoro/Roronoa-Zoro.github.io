---
layout: post
title:  "resteasy调用流程"
date:   2016-11-01 +0800
categories: RPC
tags: rpc
author: Jimmy Lee
---

* content
{:toc}

### 前言
用resteasy调用其他系统的服务,配置的http pool,为了检验一下何时释放的链接,debug了一下,对调用链路做个笔记</br>
resteasy version:3.0.19.Final


### 调用链路
* 执行前</br>
ClientProxy </br>
-> ClientInvoker </br>
-> ClientRequest </br>
-> ClientExecutionContextImpl </br>
-> ApacheHttpClient4Executor </br>
-> CloseableHttpClient </br>
-> InternalHttpClient.doExecute </br>
</br>
* 然后return
</br>
* 直到返回到ClientInvoker -> BodyEntityExtractor


### 解释说明
1.在ApacheHttpClient4Executor, 创建了释放链接和从stream中获取对象的类 </br>
2.BodyEntityExtractor, 这里检测http status是否异常，异常则抛异常,且返回异常的对象的时候，做了一个copy stream的动作，</br>
这样保证释放了当前的链接, 又保留了原始的数据流, 这样如果客户端想要进行自定义的一些操作，依然可以读到数据