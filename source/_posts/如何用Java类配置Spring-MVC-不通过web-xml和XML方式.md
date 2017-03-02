---
title: 如何用Java类配置Spring MVC(不通过web.xml和XML方式)
date: 2017-03-02 17:50:48
categories: spring
tags: spring
---
###  如何用Java类配置Spring MVC(不通过web.xml和XML方式)
#### 建立普通MAVEN项目配置POM
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ghg</groupId>
	<artifactId>chart04</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>

	<properties>
		<!-- generic -->
		<java.version>1.7</java.version>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<!-- web jsp -->
		<jsp.version>2.2</jsp.version>
		<jstl.verison>1.2</jstl.verison>
		<servlet.version>3.1.0</servlet.version>

		<!--spring -->
		<spring-framework.version>4.3.6.RELEASE</spring-framework.version>
		<!--logging -->
		<logback.version>1.2.1</logback.version>
		<slf4j.version>1.7.23</slf4j.version>
		<log4j.version>1.2.17</log4j.version>
	</properties>


	<dependencies>
		<!-- javax -->

		<dependency>
			<groupId>javax</groupId>
			<artifactId>javaee-web-api</artifactId>
			<version>7.0</version>
			<scope>provided</scope>
		</dependency>

		<!-- spring -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring-framework.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring-framework.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${spring-framework.version}</version>
		</dependency>
		<!--other web dependency -->

		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>${jsp.version}</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>${jstl.verison}</version>
		</dependency>
		<!-- servlet -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>${servlet.version}</version>
			<scope>provided</scope>
		</dependency>

		<!-- logging -->
		<!-- SLF4J和logback -->

		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${slf4j.version}</version>
		</dependency>
		<!-- logback -->
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>${logback.version}</version>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
			<version>${logback.version}</version>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-access</artifactId>
			<version>${logback.version}</version>
		</dependency>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>${log4j.version}</version>
		</dependency>
		
		<!-- jackson -->
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.8.6</version>
		</dependency>
	</dependencies>


	<build>
		<finalName>char04</finalName>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.6.1</version>
				<configuration>
					<source>${java.version}</source>
					<target>${java.version}</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>

			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-war-plugin</artifactId>
				<version>3.0.0</version>
				<configuration>
					<failOnMissingWebXml>false</failOnMissingWebXml>
				</configuration>
			</plugin>

			<!-- <plugin>
				<groupId>org.mortbay.jetty</groupId>
				<artifactId>jetty-maven-plugin</artifactId>
				<version>8.1.14.v20131031</version>
				<configuration>
					<scanIntervalSeconds>600</scanIntervalSeconds>
					<scanTargetPatterns>
						<scanTargetPattern>
							<directory>src/main/webapp</directory>
							<includes>
								<include>**/*.xml</include>
								<include>**/*.properties</include>
								<include>**/*.tld</include>
							</includes>
						</scanTargetPattern>
					</scanTargetPatterns>
					<webAppConfig>
						<contextPath>/char04</contextPath>
					</webAppConfig>
					<connectors>
						<connector implementation="org.eclipse.jetty.server.nio.SelectChannelConnector">
							<port>9090</port>
							<maxIdleTime>60000</maxIdleTime>
						</connector>
					</connectors>
				</configuration>
			</plugin> -->
		</plugins>
	</build>
</project>
```

#### 配置DispatcherServlet. 需要创建一个Web初始化类
MyMVCConfig extends WebMvcConfigurerAdapter 继承WebMvcConfigurerAdapter 
```
package com.ghg.chart04.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
import org.springframework.web.servlet.view.InternalResourceViewResolver;
import org.springframework.web.servlet.view.JstlView;

@Configuration
@EnableWebMvc
@ComponentScan("com.ghg.chart04.controller")
public class MyMVCConfig extends WebMvcConfigurerAdapter {
	@Bean
	public InternalResourceViewResolver viewResolver() {
		InternalResourceViewResolver resolver = new InternalResourceViewResolver();

		resolver.setPrefix("/WEB-INF/classes/views");
		resolver.setSuffix(".jsp");
		resolver.setViewClass(JstlView.class);
		resolver.setExposeContextBeansAsAttributes(true);
		return resolver;

	}

	// 配置静态资源的处理
	@Override
	public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
		configurer.enable(); // 对静态资源的请求转发到容器缺省的servlet，而不使用DispatcherServlet
	}

}

```
#### 配置启动配置类RootConfig

```
package com.ghg.chart04.init;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@ComponentScan(basePackages={"com.ghg.chart04"},excludeFilters={@Filter(type=FilterType.ANNOTATION,value=EnableWebMvc.class)})
public class RootConfig {

}
/**
	 * 用来配置servcie与dao 业务数据
	 */
```

#### 配置Spring MVC视图解析WebConfig.java, 需要要创建一个类继承自WebMvcConfigurerAdapter


```
package com.ghg.chart04.init;


import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

import com.ghg.chart04.config.MyMVCConfig;



public class WebInitializer extends AbstractAnnotationConfigDispatcherServletInitializer  {
/**
	 * 用来配置servcie与dao 业务数据
	 */
	@Override
	protected Class<?>[] getRootConfigClasses() {
		return new Class<?>[] { RootConfig.class }; //启动配置类
	}
	/**
	 * 用来配置MVC中的Class，也是就Controller
	 */
	@Override
	protected Class<?>[] getServletConfigClasses() {
		 return new Class<?>[] { MyMVCConfig.class };        //ָ指定Web配置类
	}
/**
	 * 将DispatcherServlet映射到/
	 */
	@Override
	protected String[] getServletMappings() {
		return new String[]{"/"};
	}

}

```
至此, 传统方式中需要通过web.xml进行配置的东西就已将全部完成有上面三个java类(WebInitializer , RootConfig, MyMVCConfig )完成. 可以开始写Controller和页面代码了

### 控制器和页面
```
package com.ghg.chart04.controller;

import javax.servlet.http.HttpServletRequest;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

	
	@RequestMapping(value="/hello")
	public Map<String, String> hello(HttpServletRequest request){
		
		Map<String, String> params = new HashMap<String, String>();
		params.put("title", "hello");
		params.put("result", "中奖了");
		return params;
	}
}

``` 

jsp
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Welcome</title>
</head>
<body>
<pre>Welcome to Spring MVC!</pre>
</body>
</html>
```
![这里写图片描述](http://img.blog.csdn.net/20170223152815739?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva28wNDkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170223152921834?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva28wNDkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20170223153434059?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva28wNDkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
