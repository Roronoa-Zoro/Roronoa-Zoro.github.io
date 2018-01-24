---
layout: post
title:  "review concurrentModificationException"
date:   2018-01-24 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


#### 一点技术细节  


### ConcurrentModificationException是怎么发生的  
拿List举例子, 一般理解是说当在这个list上通过iterator遍历, 然后在对list进行更新操作会发生. 这种理解并不准确.  


举个例子.  
一个线程通过iterator进行遍历, 然后线程T2对list[0]进行读和写. 这样其实不会发生异常.


所以确切一点的说法应该是当进行遍历的时候, 又对list进行了结构性的变更, 才会出现这个异常.  


### 翻翻源码  
##### 第一部分
1. 看看ArrayList源码, 里面有个   
```java
protected transient int modCount = 0;
```

2. 它表示的是这个list发生结构性变化的次数. 全部注释请看源码.   

##### 第二部分
1. ArrayList里面有个iterator的实现类, 具体看源码吧     
```java
private class Itr implements Iterator<E> {
	//省略
}
```

2. 这里面有个变量和检查结构性修改次数的方法  


```java
int expectedModCount = modCount;

final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
```


3. expectedModCount初始值是list里面的值, 然后Itr的next和remove对调用checkForComodification进行检查.   
4. 如果通过调用Itr的remove方法, list的modCount和Itr的expectedModCount会同时修改.   
5. 如果直接调用List的remove或者add方法, 那么List里面的modCount会变化, 这就导致了和Itr里面的expectedModCount值不一致, 异常就这么发生了.


### 参考  
jdk1.8 ArrayList源码
