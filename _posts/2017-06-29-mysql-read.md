---
layout: post
title:  "mysql read"
date:   2017-06-29 +0800
categories: SQL
tags: mysql
author: Jimmy Lee
---

* content
{:toc}

### 前言
mysql一致性读笔记
  
mysql 5.6  

### 一致性非锁定读  
读的是数据行的快照, 既历史版本的记录, 通过读undo实现, 默认读取方式  

#### read commited级别下的行为  
总是读取被锁定行的最新的一份快照数据    

#### repeatable read级别下的行为  
总是读取事务开始时的行数据版本  

### 举个例子
![](/images/mysql-read-example.jpg) 	  
val的初始值是1  


#### read commited级别下的结果
t2时刻, session A 查询返回1  
t4时刻, session B 修改该记录  
t5时刻, session A 重复查询, 返回1, 由于t4时刻的修改未提交  
t7时刻, session A 重复查询, 返回2, 是最新的数据  

#### repeatable read级别下的结果  
t2时刻, session A 查询返回1  
t4时刻, session B 修改该记录  
t5时刻, session A 重复查询, 返回1, 由于t4时刻的修改未提交  
t7时刻, session A 重复查询, 依然返回1, 快照读返回的是事务开始时的行数据  


### 总结  
不同隔离级别下, 一致性读的行为不一致, 如果需要保证数据的逻辑一致, 可以使用加锁  
select ... for update, 加一个排它锁(X锁)  
select ... lock in share mode, 加一个共享读锁(S锁)  
 
