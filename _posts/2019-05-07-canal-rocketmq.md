---
layout: post
title:  "canal-rocketmq"
date:   2019-05-07 +0800
categories: JAVA
tags: mq
author: Jimmy Lee
---

* content
{:toc}


#### canal解析的数据发送rocketmq报错  topic没有路由信息  

### 技术版本
canal: 1.1.3     
rocketmq: 4.3.2    


### 报错信息是
No route info for this topic,  XXX topic   

### debug过程  
根据错误日志提示，查到源码。定位到在下面的方法   
1)DefaultMQProducerImpl.sendSelectImpl 这个方法    
2)一路debug到MQClientInstance.updateTopicRouteInfoFromNameServer   
3)想要找到使用nameserver的地址的地方，发现地址都是直接取的，没有分隔的地方，应该方向错误了     
转换思路，可能是由于在启动的时候set nameserver地址的    
1)DefaultMQProducerImpl看它的start方法
```java
this.mQClientFactory = MQClientManager.getInstance().getAndCreateMQClientInstance(this.defaultMQProducer, rpcHook);
```  
继续往下   
```java
new MQClientInstance(clientConfig.cloneClientConfig(),
                    this.factoryIndexGenerator.getAndIncrement(), clientId, rpcHook);
```   
继续往下debug   
```java
if (this.clientConfig.getNamesrvAddr() != null) {
            this.mQClientAPIImpl.updateNameServerAddressList(this.clientConfig.getNamesrvAddr());
            log.info("user specified name server address: {}", this.clientConfig.getNamesrvAddr());
}
```   
发现根源   
```java
public void updateNameServerAddressList(final String addrs) {
        String[] addrArray = addrs.split(";");
        List<String> list = Arrays.asList(addrArray);
        this.remotingClient.updateNameServerAddressList(list);
}
```   
nameserver的地址是用;分隔的，但是canal里面配置的nameserver是通过,分隔的    
导致实际上addrArray[0]是这样一个地址"ip1:port,ip2:port",这是一个无效的地址。    
导致后续使用的时候无法连接到nameserver获取route信息报错    
