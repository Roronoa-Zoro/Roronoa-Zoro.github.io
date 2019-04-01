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



### db连接池配置
1.timeBetweenEvictionRunsMillis, 有一个检测空闲连接的线程, 它每隔这个时间执行一次, 单位毫秒   
2.minEvictableIdleTimeMillis, 一个链接在池中最小的空闲时间, 超过这个时间, 上面配置中的检测线程检测到这个链接后就干掉它   
3.removeAbandoned,这和下面的参数配合使用,用来检测链接泄露. bool类型    
4.removeAbandonedTimeout, 一个使用中的链接被删掉前的超时时间. 单位秒. 这个值要设的比应用中执行时间最长的sql的执行时间还长.    


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


### jvm 参数
1. -verbose:class, 当类冲突的时候使用, 可以打印出class是从哪个jar加载出来的
2. 查找消耗cpu或内存的线程    
step1:先top命令查看    
step2:然后top -Hp {step1中找到的pid}   
step3:shift + p 找到最耗cpu的线程    
step4:printf "%x\n" tid{step3中的pid,将需要的线程ID转换为16进制格式} (或者 pstack tid, strace -f -p tid 直接打印堆栈)    
step5:jstack -l pid{step1中的pid} > /tmp/pid.txt 
step6:根据step4中打印的名称去pid.txt查找对应的线程堆栈信息   

### rocketmq
1.client(version:4.3.1+)打印日志加 -Drocketmq.client.logUseSlf4j=true, 

### mvn 相关
1. 手动推送jar到私服  
mvn deploy:deploy-file -DgroupId=com.antgroup.zmxy -DartifactId=zmxy-sdk-java -Dversion=20170531120635 -Dclassifier=shaded -Dfile=本地jar包路径 -DrepositoryId=maven-releases -Durl=http://ip:port/repository/maven-releases/   
2. 手动安装jar到本地仓库  
mvn install:install-file -Dfile=alipay-sdk-java20171201160035.jar -DgroupId=com.alipay -DartifactId=java-sdk -Dversion=20171201 -Dpackaging=jar  

### ab结果说明
Concurrency Level:      30    
Time taken for tests:   3.438 seconds    
Complete requests:      5000   
Failed requests:        0    
Keep-Alive requests:    0    
Total transferred:      750000 bytes    
Total body sent:        1500000    
HTML transferred:       155000 bytes    
Requests per second:    1454.33 [#/sec] (mean)     1)    
Time per request:       20.628 [ms] (mean)         2)    
Time per request:       0.688 [ms] (mean, across all concurrent requests)     3)   
Transfer rate:          213.04 [Kbytes/sec] received    
                        426.07 kb/s sent    
                        639.11 kb/s total    
1)吞吐量    
2)=Time taken for tests/(Complete requests/Concurrency Level), 用户请求平均等待时间     
3)=Time taken for tests/Complete requests, 服务器平均请求时间    

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


