---
layout: post
title:  "prometheus monitor redis"
date:   2019-04-09 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


#### prometheus监控redis  


### 技术版本
grafna: 6.1.0     
redis_exporter: 0.30.0   
prometheus: 2.8.1   


### 操作步骤
1.下载redis_exporter,这是一个可执行文件,查看帮忙文档 ./redis_exporter -h   
2.监听redis cluster, 命令  ./redis_exporter -redis.addr ip1:7001,ip2:7002,ip3:7003 -redis.password xxx   
3.在prometheus服务的prometheus.yml里面添加如下信息，然后重启
```yml
- job_name: redis
    static_configs:
      - targets: ['redis_exporter_ip:9121']
        labels:
          instance: redis-cluster
```          
4.grafna 的prometheus redis json格式模板，https://grafana.com/dashboards/763    
5.导入改模板，可以直接使用763模板号，或者把json贴到grafna里面  
