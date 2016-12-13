---
layout: post
title:  "spring jpa常用查询"
date:   2016-12-13 +0800
categories: ORM
tags: jpa
author: Jimmy Lee
---

* content
{:toc}

### 前言
spring jpa常用查询

### jpa group by查询  
```java
EntityManager em = emf.createEntityManager();
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<StatEntity> q = cb.createQuery(StatEntity.class);
Root<StatEntity> c = q.from(StatEntity.class);
q.multiselect(c.get("userId"), c.get("userName"),cb.count(c.get("userId")));
q.where(cb.equal(c.get("status"), 8));
q.groupBy(c.get("userId"), c.get("userName"));
Order userIdOrder = new OrderImpl(c.get("userId"), true);
Order countOrder = new OrderImpl(cb.count(c.get("userId")), false);
q.orderBy(countOrder, userIdOrder);
System.out.println("con:" + em.createQuery(q).getResultList());
```

### 查询个别的字段
只查询StatEntity里面的id列

```java
@Query("select new com.illegalaccess.po.StatEntity(id) from StatEntity te " +
			"where te.status=:status and te.updateTime<:updateTime and te.retryCount<:retryCount")
Page<StatEntity> findAllByTaskStatus(@Param("status") Integer status,
									 @Param("updateTime")  Date updateTime,
									 @Param("retryCount") Integer retryCount,
									 Pageable pageable);
``` 
在StatEntity里面添加只包含id的构造函数,则查询只查询id列  
若想查询id,status,则在StatEntity里面添加如下构造函数  
```java
public StatEntity(Long id, Integer status) {
	this.id = id;
	this.status = status;
}
```

上面的查询换成如下格式

```java
@Query("select new com.illegalaccess.po.StatEntity(id,status) from StatEntity te " +
			"where te.status=:status and te.updateTime<:updateTime and te.retryCount<:retryCount")
Page<StatEntity> findAllByTaskStatus(@Param("status") Integer status,
									 @Param("updateTime")  Date updateTime,
									 @Param("retryCount") Integer retryCount,
									 Pageable pageable);
``` 




