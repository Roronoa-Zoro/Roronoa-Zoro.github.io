---
layout: post
title:  "redis配置参数笔记"
date:   2017-03-06 +0800
categories: REDIS
tags: redis
author: Jimmy Lee
---

* content
{:toc}

### 前言
redis配置参数笔记

测试的redis版本是2.8.12

### hashes
当hash里面的元素数量很少的时候，在redis里面会特殊处理，数据结构是长度预定义的，key-value对形式的线性数组

用这种方式进行处理是为了权衡cpu和内存


当entry数量很多时，才会转成使用hash-table这种数据结构存储


想开启这个特性，需要设置下面的2个参数(官方文档里面还是写的zipmap)
```
hash-max-ziplist-entries 256  
hash-max-ziplist-value 64
```

entries这个参数说明是这个hash里面元素的个数，*-value是说这个hash里面的单一的value大小超过1024字节时，变成hashtable存储

test{  
k1:aaagggggggggg (k1占2个字节，value占13字节)  
k2:bbbbbbbbbbbbbb (k1占2个字节，value占14字节)  
k3:dfgdfgdfgerterterdfgdfg (k1占2个字节，value占23字节)  
k4:hhhhh (k1占2个字节，value占5字节)  
k5:fghcbcvbcvbcvb (k1占2个字节，value占14字节)  
k6:11111111wwwwwwwwwweeeeerrrrtttdddxcvxcvxcvxcvxvxcvxcvxcvxvxcvqopp (k1占2个字节，value占65字节)  
}  

先添加了k1~k5，5个entry，然后使用debug object test，查看

127.0.0.1:6379> debug object test
Value at:000007EB1F54F350 refcount:1 encoding:ziplist serializedlength:130 lru:12389993 lru_seconds_idle:5

然后添加k5，value的字节数逐渐增长到65，当长度为64时，依然是ziplist结构，value大小变成65字节时，结果如下

127.0.0.1:6379> debug object test
Value at:000007EB1F54F350 refcount:1 encoding:hashtable serializedlength:133 lru:12390003 lru_seconds_idle:4


参考文档  
[官方文档](https://redis.io/topics/memory-optimization#using-hashes-to-abstract-a-very-memory-efficient-plain-key-value-store-on-top-of-redis)