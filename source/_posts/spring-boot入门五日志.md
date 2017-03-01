---
title: spring-boot入门五日志
date: 2017-03-01 16:20:19
categories: spring-boot
tags: spring-boot
---

### spring-boot入门五日志

##### 日志设置
spring boot默认使用logback做为日志框架
Spring boot索马里Java Util loggin,log4j,log4j2,logback日志框架,
无论使用哪种日志框架，Spring-boot已经为当前使用的日志的控制台输出及输入文件做好了配置

需要改变，要自己配置
```
# LOGGING
logging.config=classpath:config/logback.xml
```
logback.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds">
	<contextName>bt</contextName>
	<property name="log_base" value="${catalina.base}/logs/bt" />
	<!-- 控制台输出日志 -->
	<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>%date{HH:mm:ss} %-5level %logger{0} - %msg%n</pattern>
		</encoder>
	</appender>
	<appender name="rollingFile"
		class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>${log_base}/log.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>${log_base}/log%d{yyyy-MM-dd}.log</fileNamePattern>
			<maxHistory>90</maxHistory>
		</rollingPolicy>
		<encoder>
			<pattern>%date %-5level %logger{0} - %msg%n</pattern>
			<charset>UTF-8</charset>
		</encoder>
	</appender>
	<appender name="operateLogFile"
		class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>${log_base}/operate/optlog.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>${log_base}/operate/optlog%d{yyyy-MM-dd}.log</fileNamePattern>
		</rollingPolicy>
		<encoder>
			<pattern>%date %-5level - %msg%n</pattern>
			<charset>UTF-8</charset>
		</encoder>
	</appender>

	<!-- project default level -->
	<logger name="org.springframework" level="warn" />
	<logger name="org.springframework.jdbc" level="warn" />
	<logger name="operateLog" level="debug" additivity="false">
		<appender-ref ref="operateLogFile" />
	</logger>
	<root level="info">
		<appender-ref ref="rollingFile" />
		<appender-ref ref="console"/>
	</root>
</configuration>
```