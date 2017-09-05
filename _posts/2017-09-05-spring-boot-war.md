---
layout: post
title:  "spring boot package war"
date:   2017-09-05 +0800
categories: JAVA
tags: spring boot
author: Jimmy Lee
---

* content
{:toc}

### 前言
spring boot打包成war,切切换profile
  
spring boot: 1.5.6
spring cloud: Dalston.SR2 

### 打包成war  
基于spring cloud的开发，默认是打包成jar的，有些情况我们需要打成war然后部署到tomcat下，下面的方式可以打成war并且使用maven切换profile

1. 修改pom
```
<packaging>war</packaging>
```  

2. 排除内嵌的tomcat
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
		<exclusions>
			<exclusion>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-tomcat</artifactId>
			</exclusion>
		</exclusions>
</dependency>
```

3. 根据环境确定内嵌tomcat的scope
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-tomcat</artifactId>
	<scope>${tomcat.scope}</scope>
</dependency>
```

4. 添加maven里面的profile
```xml
<profiles>
	<profile>
		<id>dev</id>
		<properties>
			<tomcat.scope>compile</tomcat.scope>
			<project.profile.active>dev</project.profile.active>
		</properties>
		<activation>
			<activeByDefault>true</activeByDefault>
		</activation>
	</profile>
	<profile>
		<id>mock</id>
		<properties>
			<tomcat.scope>compile</tomcat.scope>
			<project.profile.active>mock</project.profile.active>
		</properties>
	</profile>
	<profile>
		<id>uat</id>
		<properties>
			<tomcat.scope>provided</tomcat.scope>
			<project.profile.active>uat</project.profile.active>
		</properties>
	</profile>
	<profile>
		<id>prod</id>
		<properties>
			<tomcat.scope>provided</tomcat.scope>
			<project.profile.active>prod</project.profile.active>
		</properties>
	</profile>
</profiles>
```

5. 添加maven打包插件
```
<build>
	<finalName>ur-name</finalName>
	<resources>
		<resource>
			<directory>src/main/resources</directory>
			<filtering>true</filtering>
		</resource>
	</resources>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-war-plugin</artifactId>
			<version>3.1.0</version>
			<configuration>
				<failOnMissingWebXml>false</failOnMissingWebXml>
			</configuration>
		</plugin>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```
如果不添加第二个插件，项目会因为没有web.xml为打包报错

6. 修改启动类
```java
@EnableDiscoveryClient
@SpringBootApplication
public class Application extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(
			SpringApplicationBuilder application) {
		return application.sources(Application.class);
	}
	
	public static void main(String[] args) {
		new SpringApplicationBuilder(Application.class).web(true).run(args);
	}

}
```

7. 修改spring cloud config配置
```
spring:
  application:
    name: gateway-app
  cloud:
    config:
      discovery:
        enabled: true
        service-id: payment-config-server
      label: master
      profile: @project.profile.active@
```
profile变量名需要使用2个@引起来,service-id是config server的名字

 
 