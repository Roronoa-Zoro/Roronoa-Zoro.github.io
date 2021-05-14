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
1和2的时间和要小于mysql的wait_timeout    


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
1. -verbose:class, 当类冲突的时候使用, 可以打印出class是从哪个jar加载出来的。  
2. -Djava.endorsed.dirs，ava提供了endorsed技术：
关于 endorsed ：可以的简单理解为 -Djava.endorsed.dirs 指定的目录面放置的jar文件，将有覆盖系统API的功能。但是能够覆盖的类是有限制的，其中不包括java.lang包中的类(出于安全的考虑)。

为什么必须使用 endorsed 进行替换 jdk 中的类呢？
因为java是采用双亲委派机制进行加载class类的。而jdk提供的类只能由类加载器Bootstrap进行加载。如果你想要在应用程序中替换掉jdk中的某个类是无法做到的，所以java提供了endorsed来达到你想要替换到系统中的类。


### linux
#### 检查CPU耗时高的命令
1. 查找消耗cpu或内存的线程    
step1:先top命令查看    
step2:然后top -Hp {step1中找到的pid}   
step3:shift + p 找到最耗cpu的线程    
step4:printf "%x\n" tid{step3中的pid,将需要的线程ID转换为16进制格式} (或者 pstack tid, strace -f -p tid 直接打印堆栈)    
step5:jstack -l pid{step1中的pid} > /tmp/pid.txt 
step6:根据step4中打印的名称去pid.txt查找对应的线程堆栈信息   

2.atop & htop使用    
3.perf top -g -p pid,可以先到命令级/方法级调用, 上下键可以选择不同命令, enter或者e展开调用链, c键收回调用链展示   

#### 检查磁盘命令
1. df磁盘空间查看    
df可以查看一级文件夹大小、使用比例、档案系统及其挂入点。   
df -Th    
2. du详细磁盘使用分析   
du命令用来查看目录或文件所占用磁盘空间的大小。常用选项组合为：du -sh 目录。     
-h：以人类可读的方式显示。    
-a：显示目录占用的磁盘空间大小，还要显示其下目录和文件占用磁盘空间的大小。     
-s：只显示目录占用的磁盘空间大小，不显示其子目录和文件占用的磁盘空间大小。     
-c：显示几个目录或文件占用的磁盘空间大小，还要统计它们的总和。     
--apparent-size：显示目录或文件自身的大小。    
-l ：统计硬链接占用磁盘空间的大小。     
-L：统计符号链接所指向的文件占用的磁盘空间大小      
默认情况下，du不显示目录下文件占用磁盘空间的信息，但它会显示其下子目录占用磁盘空间的信息；而使用-s选项以后，只显示xx目录占用磁盘空间的大小。    
du --apparent-size：显示文件或目录自身大小，而不是它们占用的磁盘空间大小。文件或目录占用磁盘空间的大小与它们自身大小有时候并非完全一致；这种现象非linux所独有，windows里也是如此。    
du -c：使用此选项时，不仅显示几个文件或目录各自占用磁盘空间的大小，还统计它们的总和。     

du命令还可以对结果进行排序，以方便快速找出占用空间最多目录。    
du -s 目录 | sort -rn        #这是按字节排序     
du -sh 目录 | sort -rn        #这是按兆（M）来排序      
#由于-sh大小显示看起来是乱的，因此建议使用du -s|sort -nr      

du -s 目录 | sort -rn | head        #选出排在前面的10个    
du -s 目录 | sort -rn | tail        #选出排在后面的10个    

du -h --max-depth=1 /home  仅列出home目录下面所有的一级目录文件大小；    
du -h --max-depth=1 /home/*   列出home下面所有一级目录的一级目录文件大小。    


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


