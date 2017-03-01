---
title: spring-boot入门一环境搭建及hellospringboot
date: 2017-03-01 09:20:44
categories: spring-boot
tags: spring-boot
---

### spring-boot入门一环境搭建及hellospringboot

##### 环境
本人使用myeclipse 10.6,JDK7.0,MAVEN 3.3.9

##### 新建MAVEN普通项目添加依赖-MAVEN 插件
官网<http://projects.spring.io/spring-boot/#quick-start>
	
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ghg</groupId>
	<artifactId>boot</artifactId>
	<version>0.0.1-SNAPSHOT</version>


	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.1.RELEASE</version>
	</parent>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
		<!--spring-boot maven  -->
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

#### 测试控制器同时添加main方法
```
package com.ghg.boot.controller;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class HelloController {

	
	@RequestMapping(value="/hello")
	public String hello(){
		
		return "Hello Spring Boot!";
	}
	
	public static void main(String[] args) {
		SpringApplication.run(HelloController.class, args);
	}
}

```

#### 运行

* MAVEN 运行

```
mvn spring-boot:run
```
```
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.1.RELEASE)

2017-03-01 09:27:30.965  INFO 8040 --- [           main] com.ghg.boot.controller.HelloController  : Starting HelloController on SC-201607041015 with PID 8040 (D:\myeclipsework\boot\target\classes started by Administrator in D:\myeclipsework\boot)
2017-03-01 09:27:30.969  INFO 8040 --- [           main] com.ghg.boot.controller.HelloController  : No active profile set, falling back to default profiles: default
2017-03-01 09:27:31.014  INFO 8040 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@563d35d9: startup date [Wed Mar 01 09:27:31 CST 2017]; root of context hierarchy
2017-03-01 09:27:31.638  INFO 8040 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration' of type [class org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2017-03-01 09:27:31.753  INFO 8040 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'validator' of type [class org.springframework.validation.beanvalidation.LocalValidatorFactoryBean] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2017-03-01 09:27:32.128  INFO 8040 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2017-03-01 09:27:32.137  INFO 8040 --- [           main] o.apache.catalina.core.StandardService   : Starting service Tomcat
2017-03-01 09:27:32.138  INFO 8040 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.11
2017-03-01 09:27:32.208  INFO 8040 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2017-03-01 09:27:32.208  INFO 8040 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1196 ms
2017-03-01 09:27:32.358  INFO 8040 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Mapping servlet: 'dispatcherServlet' to [/]
2017-03-01 09:27:32.362  INFO 8040 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2017-03-01 09:27:32.362  INFO 8040 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2017-03-01 09:27:32.362  INFO 8040 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2017-03-01 09:27:32.362  INFO 8040 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2017-03-01 09:27:32.663  INFO 8040 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@563d35d9: startup date [Wed Mar 01 09:27:31 CST 2017]; root of context hierarchy
2017-03-01 09:27:32.734  INFO 8040 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/hello]}" onto public java.lang.String com.ghg.boot.controller.HelloController.hello()
2017-03-01 09:27:32.739  INFO 8040 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2017-03-01 09:27:32.739  INFO 8040 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2017-03-01 09:27:32.778  INFO 8040 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-03-01 09:27:32.778  INFO 8040 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-03-01 09:27:32.833  INFO 8040 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2017-03-01 09:27:33.102  INFO 8040 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-03-01 09:27:33.156  INFO 8040 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2017-03-01 09:27:33.161  INFO 8040 --- [           main] com.ghg.boot.controller.HelloController  : Started HelloController in 2.43 seconds (JVM running for 5.557)

```

#### 访问
http://127.0.0.1:8080/hello
就可以看到结果


#### 更换Banner

就是这个
```
/\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
```
到
<http://patorjk.com/software/taag/>
生成字符串
在src/main/resources 下新建一个banner.txt，文件，将上面生成的字符复制到banner.txt中，再次启动就变为自己的log了
```
[INFO] --- spring-boot-maven-plugin:1.5.1.RELEASE:run (default-cli) @ boot ---
           __                                     
          |  \                                    
  ______  | $$____    ______    _______  _______  
 /      \ | $$    \  /      \  /       \|       \ 
|  $$$$$$\| $$$$$$$\|  $$$$$$\|  $$$$$$$| $$$$$$$\
| $$  | $$| $$  | $$| $$  | $$| $$      | $$  | $$
| $$__| $$| $$  | $$| $$__| $$| $$_____ | $$  | $$
 \$$    $$| $$  | $$ \$$    $$ \$$     \| $$  | $$
 _\$$$$$$$ \$$   \$$ _\$$$$$$$  \$$$$$$$ \$$   \$$
|  \__| $$          |  \__| $$                    
 \$$    $$           \$$    $$                    
  \$$$$$$             \$$$$$$                     
2017-03-01 09:36:14.028  INFO 12032 --- [           main] com.ghg.boot.controller.HelloController  : Starting HelloController on SC-201607041015 with PID 12032 (D:\myeclipsework\boot\target\classes started by Administrator in D:\myeclipsework\boot)
2017-03-01 09:36:14.033  INFO 12032 --- [           main] com.ghg.boot.controller.HelloController  : No active profile set, falling back to default profiles: default
2017-03-01 09:36:14.080  INFO 12032 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@48c8997c: startup date [Wed Mar 01 09:36:14 CST 2017]; root of context hierarchy
2017-03-01 09:36:14.717  INFO 12032 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration' of type [class org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2017-03-01 09:36:14.803  INFO 12032 --- [           main] trationDele
```


#### 关于@SpringBootApplication注解
```
/*
 * Copyright 2012-2017 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.boot.autoconfigure;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.context.TypeExcludeFilter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.core.annotation.AliasFor;

/**
 * Indicates a {@link Configuration configuration} class that declares one or more
 * {@link Bean @Bean} methods and also triggers {@link EnableAutoConfiguration
 * auto-configuration} and {@link ComponentScan component scanning}. This is a convenience
 * annotation that is equivalent to declaring {@code @Configuration},
 * {@code @EnableAutoConfiguration} and {@code @ComponentScan}.
 *
 * @author Phillip Webb
 * @author Stephane Nicoll
 * @since 1.2.0
 */
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

	/**
	 * Exclude specific auto-configuration classes such that they will never be applied.
	 * @return the classes to exclude
	 */
	@AliasFor(annotation = EnableAutoConfiguration.class, attribute = "exclude")
	Class<?>[] exclude() default {};

	/**
	 * Exclude specific auto-configuration class names such that they will never be
	 * applied.
	 * @return the class names to exclude
	 * @since 1.3.0
	 */
	@AliasFor(annotation = EnableAutoConfiguration.class, attribute = "excludeName")
	String[] excludeName() default {};

	/**
	 * Base packages to scan for annotated components. Use {@link #scanBasePackageClasses}
	 * for a type-safe alternative to String-based package names.
	 * @return base packages to scan
	 * @since 1.3.0
	 */
	@AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
	String[] scanBasePackages() default {};

	/**
	 * Type-safe alternative to {@link #scanBasePackages} for specifying the packages to
	 * scan for annotated components. The package of each class specified will be scanned.
	 * <p>
	 * Consider creating a special no-op marker class or interface in each package that
	 * serves no purpose other than being referenced by this attribute.
	 * @return base packages to scan
	 * @since 1.3.0
	 */
	@AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
	Class<?>[] scanBasePackageClasses() default {};

}

```
@SpringBootApplication等同于
```
@Configuration
@ComponentScan
@EnableAutoConfiguration

```
组合
关闭特定的配置，使用exclude参数
```

@SpringBootApplication(exclude={DataSourceConnectionSource.class})
```

