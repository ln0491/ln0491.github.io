---
title: spring-boot入门六springboot + devtools热部署
date: 2017-03-03 14:47:20
categories: spring-boot
tags: spring-boot
---


#### Developer tools
pring-boot-devtools 是一个为开发者服务的一个模块，其中最重要的功能就是自动应用代码更改到最新的App上面去。原理是在发现代码有更改之后，重新启动应用，但是比速度比手动停止后再启动还要更快，更快指的不是节省出来的手工操作的时间。

只需要添加依赖即可
```
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
```

测试方法：

* 修改类-->保存：应用会重启
* 修改配置文件-->保存：应用会重启


#### 排除静态资源
Excluding resources
```
Certain resources don’t necessarily need to trigger a restart when they are changed. For example, Thymeleaf templates can just be edited in-place. By default changing resources in /META-INF/maven, /META-INF/resources ,/resources ,/static ,/public or /templates will not trigger a restart but will trigger a live reload. If you want to customize these exclusions you can use the spring.devtools.restart.exclude property. For example, to exclude only /static and /public you would set the following:
```
建议我们将静态资源放在以下目录
* /META-INF/maven
* /META-INF/resources
* /resources
* /static
* /public
* /templates

根据个人喜好，可以放置，
在application.properties中配置
```
spring.devtools.restart.exclude=static/**,public/**
```
静态资源更改好，应用不会重启

spring默认，已经设置了
```
spring.devtools.restart.exclude=META-INF/maven/**,META-INF/resources/**,resources/**,static/**,public/**,templates/**,**/*Test.class,**/*Tests.class,git.properties # Patterns that should be excluded from triggering a full restart.
spring.devtools.restart.poll-interval=1000 # Amount of time (in milliseconds) to wait between polling for classpath changes.

```