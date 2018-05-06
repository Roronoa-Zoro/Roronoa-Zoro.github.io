---
layout: post
title:  "经常用到的各种命令"
date:   2016-12-07 +0800
categories: 默认分类
tags: 默认分类
author: Jimmy Lee
---

* content
{:toc}

### 前言
日常使用的各种命令

### mysql 相关命令 ###
1. 查看表有哪些索引  
SHOW INDEX FROM tbl_name

2. 检查mysql缓存是否打开  

```sql
show variables like '%query_cache%';
``` 

Variable_name       			|Value   
have_query_cache				|YES  
query_cache_limit				|1048576  
query_cache_min_res_unit		|4096  
query_cache_size				|0  
query_cache_type				|OFF  
query_cache_wlock_invalidate	|OFF  


### IDEA 相关命令
1.跳转到某一行  
Ctrl + G  
2.当前类的方法和变量  
Ctrl + F12  
3.替换文本  
Ctrl + R  
4. 定位class   
在左侧project view的scroll from source按钮   
5. exception breakpoint    
5.1 在debugger里面,打开view breakpoints(Ctrl + Shift + F8), 点击左上角绿色+, 选择java exception breakpoints, 输入一个异常类, 然后打钩, 点Done, 这样当发生这个异常的地方就能断点了


### hibernate 相关 
1.打印sql参数,添加如下配置

```xml
<prop key="hibernate.show_sql">true</prop>  
```

log的配置

```xml
<logger name="org.hibernate.SQL" level="trace"/>
<logger name="org.hibernate.type" level="trace"/>
```

### git 命令
1. 想要提交某个空目录  
可以创建一个.gitkeep文件,添加一些说明  
```
# ignore everything in this directory  
*  
# except this file! .gitkeep
``` 

这样在eclipse打开后,这个目录还是显示是空的

2. 切换到远程分支
git branch -a  
* master   
  remotes/origin/HEAD -> origin/master   
  remotes/origin/dev    
  remotes/origin/master    
想要切换到dev分支    
git checkout -b dev origin/dev    
则在本地创建了名称为dev的分支, 和远程的dev一致    

3. 把代码从一个gitlab迁移到另一个gitlab    
从gitlab A clone代码   
切换到想要推送的分支(假设叫dev)   
在新的gitlab B新建同名的项目   
设置新的gitlab地址， git remote add origin ${gitlab B的地址}   
推送到新的gitlab，git push ${gitlab B的地址} -all (--all 会推送全部分支，不加 则只推送当前分支)    




### markdown 相关语法
1.换行  
连续2个空格  
2.语法高亮     
```后面加语言  
支持的语言：actionscript, apache, bash, clojure, cmake, coffeescript, cpp, cs, css, d, delphi, django, 
erlang, go, haskell, html, http, ini, java, javascript, json, lisp, lua, markdown, matlab, nginx, 
objectivec, perl, php, python, r, ruby, scala, smalltalk, sql, tex, vbscript, xml  
参考  
[https://segmentfault.com/markdown](https://segmentfault.com/markdown)
