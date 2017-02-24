---
title: logback常用配置详解
date: 2017-02-24 09:55:10
categories: logback	
tags: logback
---

### logback常用配置详解

#### 什么是 logback
Logback 为取代 log4j 而生.
Logback 由 log4j 的创立者 Ceki Gülcü 设计。以十多年设计工业级记录系统的经验为基
础，所创建的 logback 比现有任何记录系统更快、占用资源更少，有时差距非常大。
Logback 提供独特而实用的特性，比如 Marker、参数化记录语句、条件化堆栈跟踪和强
大的事件过滤功能。以上列出的仅仅是 logbook 实用特性的一小部分。
对于自身的错误报告，logback 依赖状态（Status）对象，状态对象极大地简化了故障查
找。你也许想在上下文中使用状态对象而不是记录。
Logback-core 附带了 Joran，Joran 是个强大的、通用的配置系统，你可以在自己的项目
里使用 Joran 以获得巨大的作用

#### 第一步集成
Logback-classic 依赖 slf4j-api.jar 和 logback-core.jar。
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ghg</groupId>
	<artifactId>logback</artifactId>
	<version>0.0.1-SNAPSHOT</version>


	<dependencies>
		<!--slf4j -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>1.7.23</version>
		</dependency>
		<!-- logback -->
		<!-- classic会自动引入core -->
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>1.2.1</version>
		</dependency>
		<!--非必需  -->
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
			<version>1.2.1</version>
		</dependency>
		<!--非必需  -->
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-access</artifactId>
			<version>1.2.1</version>
		</dependency>

	</dependencies>
	<build />
</project>
```

简单输出
```
package com.ghg.logback.demo1;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
	//声明org.slf4j.Logger 通过工厂获取
	private static final Logger logger = LoggerFactory.getLogger(HelloWorld.class);
	
	public static void main(String[] args) {
		
		logger.debug("Hello ,World!.");
		logger.info("Hello ,World!.");
		logger.warn("Hello ,World!.");
		logger.error("Hello ,World!.");
		

	}

}

```
SLF4J API 定义的 Logger 类和 LoggerFactory 类，更明确地说是
定义在 org.slf4j 包里的两个类
结果:
```
10:05:08.086 [main] DEBUG com.ghg.logback.demo1.HelloWorld - Hello ,World!.
10:05:08.089 [main] INFO com.ghg.logback.demo1.HelloWorld - Hello ,World!.
10:05:08.089 [main] WARN com.ghg.logback.demo1.HelloWorld - Hello ,World!.
10:05:08.089 [main] ERROR com.ghg.logback.demo1.HelloWorld - Hello ,World!.
```
Logback 可以通过内置的状态系统来报告其内部状态。通过 StatusManager 组件可以访
问 logback 生命期内发生的重要事件。目前，我们调用 StatusPrinter 类的 print()方法来打印
logback 的内部状态。
```
package com.ghg.logback.demo1;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.core.util.StatusPrinter;

public class HelloWorld {
	//声明org.slf4j.Logger 通过工厂获取
	private static final Logger logger = LoggerFactory.getLogger(HelloWorld.class);
	
	/**
	 * The main method.
	 *
	 * @param args the arguments
	 */
	public static void main(String[] args) {
		LoggerContext loggerContext = (LoggerContext) LoggerFactory.getILoggerFactory();
		logger.debug("Hello ,World!.");
		
		logger.info("Hello ,World!.");
		
		logger.warn("Hello ,World!.");
		
		logger.error("Hello ,World!.");
	
		StatusPrinter.print(loggerContext);
		

	}

}

```
结果
```
10:09:16.054 [main] DEBUG com.ghg.logback.demo1.HelloWorld - Hello ,World!.
10:09:16.056 [main] INFO com.ghg.logback.demo1.HelloWorld - Hello ,World!.
10:09:16.056 [main] WARN com.ghg.logback.demo1.HelloWorld - Hello ,World!.
10:09:16.056 [main] ERROR com.ghg.logback.demo1.HelloWorld - Hello ,World!.
10:09:16,026 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback-test.xml]
10:09:16,026 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.groovy]
10:09:16,026 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.xml]
10:09:16,029 |-INFO in ch.qos.logback.classic.BasicConfigurator@557485ac - Setting up default configuration.
```
结构:
* 第一个字段是自程序启动以来的逝去时间，单位是毫秒。
* 第二个地段发出记录请求的线程。
* 第三个字段是记录请求的级别。
* 第四个字段是与记录请求关联的 logger 的名称。
* “-”之后是请求的消息文字。

Logback 说它没有找到配置文件 logback-test.xml 、logback.groovy和 logback.xml，于是用默
认策略进行配置，即用一个基本的 ConsoleAppender。Appender 类可被视为输出目的地的。
Appender 包含许多不同类型的目的地，包括控制台、文件、Syslog、TCP 套接字、JMS 和
其他。用户可以很容易地自定义 Appender。


#### 在应程序里启用记录的三个必需步骤如下

1.  配置 logback 环境。引入依赖，JAR之类
2.  在每个需要执行记录的类里，调用 org.slf4j.LoggerFactory 类的 getLogger()方法获
取一个 Logger 实例，以当前类名或类本身作为参数。

3.  调用取得的 logger 实例的打印方法，即 debug()、info()、warn()和 error()，把记录
输出到配置里的各 appender。


#### 体系结构

##### logback  的体系结构
Logback 的基本结构充分通用，可应用于各种不同环境。目前，logback 分为三个模块：
Core、Classic 和 Access
Core模块是其他两个模块的基础。Classic 模块扩展了core模块。Classic 模块相当于 log4j
的显著改进版。Logback-classic 直接实现了 SLF4J API，因此你可以在 logback 与其他记录
系统如 log4j 和 java.util.logging (JUL)之间轻松互相切换。Access 模块与 Servlet 容器集成，
提供 HTTP 访问记录功能

这里，“logback”代表 logback-classic 模块。引入时只需要引入这个就可以使用logback了


##### Logger 、Appender 和 和 Layout
Logback 建立于三个主要类之上：Logger、Appender 和 Layout。这三种组件协同工作，
使开发者可以按照消息类型和级别来记录消息，还可以在程序运行期内控制消息的输出格式
和输出目的地。
Logger类是logback-classic 模块的一部分，而Appender和Layout接口来自logback-core。
作为一个多用途模块，logback-core 不包含任何 logger。

Logger 是命名了的实体:
如果 logger  的名称带上一个点号后是另外一个 logger  的名称的前缀，那么， 前者 就被
称为 后者的祖先。 如果 logger  与代 其后代 logger  之间没有其他祖先， 那么，前者就被称为子
logger  之 父。
比如，名为“com.foo"”的 logger 是名为“com.foo.Bar”之父。同理， “java”是“java.util"”
之父，也是“java.util.Vector”的祖先。
根 logger 位于 logger 等级的最顶端，它的特别之处是它是每个层次等级的共同始祖。
如同其他各 logger，根 logger 可以通过其名称取得，如下所示：
```

		Logger logger =LoggerFactory.getLogger(org.slf4j.Logger.ROOT_LOGGER_NAME);
		
		logger.error(logger.getName());
		
```
结果
```
10:17:54.385 [main] ERROR ROOT - ROOT
```

其他所有 logger 也通过 org.slf4j.LoggerFactory 类的静态方法 getLogger 取得。getLogger
方法以 logger 名称为参数

##### 有效 级别（Level ） 即 级别 继承

Logger 可以被分配级别。级别包括：TRACE、DEBUG、INFO、WARN 和 ERROR，定
义于 ch.qos.logback.classic.Level 类。注意在 logback 里，Level 类是 final 的，不能被继承，
Marker 对象提供了更灵活的方法。

为确保所有 logger 都能够最终继承一个级别，根 logger 总是有级别，默认情况下，这
个级别是 DEBUG

logger L 的有效级别 等于其层次等级里的第一个非 null  级别， 顺序是从 从 L 开始， 向上
直至根 根 logger

#### logback配置

Logback 可以通过编程式配置，或用 XML 格式的配置文件进行配置

Logback 采取下面的步骤进行自我配置：
1.  尝试在 classpath 下查找文件 logback-test.xml；
2.  如果文件不存在，则查找文件 logback.xml；
3.  如果两个文件都不存在，logback 用 BasicConfigurator 自动对自己进行配置，这会
导致记录输出到控制台。
第三步也是最后一步是为了在缺少配置文件时提供默认（但基本的）记录功能

##### 自动配置

不需要写配置文件直接使用，如开头的例子，日志会输出到控制台
```
package com.ghg.logback.demo1;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.core.util.StatusPrinter;

public class HelloWorld {
	//声明org.slf4j.Logger 通过工厂获取
	private static final Logger logger = LoggerFactory.getLogger(HelloWorld.class);
	
	/**
	 * The main method.
	 *
	 * @param args the arguments
	 */
	public static void main(String[] args) {
		LoggerContext loggerContext = (LoggerContext) LoggerFactory.getILoggerFactory();
		logger.debug("Hello ,World!.");
		
		logger.info("Hello ,World!.");
		
		logger.warn("Hello ,World!.");
		
		logger.error("Hello ,World!.");
	
		StatusPrinter.print(loggerContext);
		
		Logger logger =LoggerFactory.getLogger(org.slf4j.Logger.ROOT_LOGGER_NAME);
		
		logger.error(logger.getName());
		

	}

}

```

##### logback-test.xml 或 或 logback.xml  自动 配置

如果 classpath 里有 logback-test.xml 或 logback.xml，logback 会试图用它进
行自我配置,在resources目录下，建立logback.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
	<!--指定输出目的地为控制台 ch.qos.logback.core.ConsoleAppender name可以随意写 -->
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<!--指定输出规则 -->
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36}- %msg%n
			</pattern>
		</encoder>
	</appender>
	<!--过滤输出级别，在这个之上的才会输出  -->
	<root level="info">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```
代码
```
package com.ghg.logback.demo1;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.core.util.StatusPrinter;

public class HelloWorld1 {
	//声明org.slf4j.Logger 通过工厂获取
	private static final Logger logger = LoggerFactory.getLogger(HelloWorld1.class);
	
	/**
	 * The main method.
	 *
	 * @param args the arguments
	 */
	public static void main(String[] args) {
	
		LoggerContext loggerContext = (LoggerContext) LoggerFactory.getILoggerFactory();
		logger.debug("Hello ,World!.");
		
		logger.info("Hello ,World!.");
		
		logger.warn("Hello ,World!.");
		
		logger.error("Hello ,World!.");
	
		
		StatusPrinter.print(loggerContext);
	
	}

}

```
结果
```
10:39:11.621 [main] INFO  com.ghg.logback.demo1.HelloWorld1- Hello ,World!.
10:39:11.623 [main] WARN  com.ghg.logback.demo1.HelloWorld1- Hello ,World!.
10:39:11.623 [main] ERROR com.ghg.logback.demo1.HelloWorld1- Hello ,World!.
.....省略
10:41:50,877 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback-test.xml]
10:41:50,877 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.groovy]
10:41:50,877 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Found resource [logback.xml] at [file:/D:/myeclipsework/logback/target/classes/logback.xml]
10:41:50,925 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - debug attribute not set
10:41:50,926 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - About to instantiate appender of type [ch.qos.logback.core.ConsoleAppender]
10:41:50,932 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Naming appender as [STDOUT]
10:41:50,937 |-INFO in ch.qos.logback.core.joran.action.NestedComplexPropertyIA - Assuming default type [ch.qos.logback.classic.encoder.PatternLayoutEncoder] for [encoder] property
10:41:50,974 |-INFO in ch.qos.logback.classic.joran.action.RootLoggerAction - Setting level of ROOT logger to INFO
10:41:50,974 |-INFO in ch.qos.logback.core.joran.action.AppenderRefAction - Attaching appender named [STDOUT] to Logger[ROOT]
10:41:50,975 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - End of configuration.
10:41:50,976 |-INFO in ch.qos.logback.classic.joran.JoranConfigurator@14874647 - Registering current configuration as safe fallback point


```
发现只有INFO之上的才输出，DEBUG并没有输出,同时也提示找到了Found resource [logback.xml]

可以不用从代码里调用 StatusPrinter，而是在配置文件里进行相关配置，即使没有出现
错误。方法是，设置 configuration 元素的 debug 属性为 true。请注意 debug 属性只与状态数
据有关，它不影响 logback 的配置，更不会影响记录级别
##### debug模式，
```
<configuration debug="true">
```
加个这段就不需要使用StatusPrinter进行打印了
把 configuration 元素的 debug 属性设为 true 后，会输出状态信息，但是前提是：
1.  找到了配置文件；
2.  配置文件是格式化良好的 XML

##### 配置文件修改后自动重新加载
如果设置成自动重新加载，logback-classic 会扫描配置文件里的变化，并且当发生变化
后进行重新配置。设置访方法是设 configuration 元素的 scan 属性为 true。
```
<configuration debug="true" scan="true">
...
</configuration>
```
默认情况下，每隔一分钟扫描一次。configuration 元素的 scanPeriod 属性控制扫描周期，
其值可以带时间单位，包括：milliseconds、seconds、minutes 和 hours。
指定不同的扫描周期
```
<configuration debug="true" scan="true" scanPeriod="30 seconds">
...
</configuration>
```
如果没写明时间单位，则默认为毫秒

#####  查看状态消息

Logback 把内部数据放在一个 StatusManager 对象里，并通过 LoggerContext 访问
StatusManager 通过 logback 上下文来访问所有数据对象。为把内存占用保持在合理的范
围内，默认的 StatusManager 实现将状态消息按头和尾两部分存储。头部存储开始的 H 条状
态消息，尾部存储后面的 T 条消息。现在的 H=T=150，将来或许会改变

Logback-classic 带了一个叫 ViewStatusMessagesServlet 的 Servlet，它以 HTML 表格的格
式打印与当前 LoggerContext 关联的 StatusManager 的内容


![logbackstatuslook.png](https://ln0491.github.io/img/logbackstatuslook.png)

要加到自己的 web 应用程序里，可以在 WEB-INF/web.xml 里添加如下内容
```
<servlet>
		<servlet-name>ViewStatusMessages</servlet-name>
		<servlet-class>ch.qos.logback.classic.ViewStatusMessagesServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>ViewStatusMessages</servlet-name>
		<url-pattern>/lbClassicStatus</url-pattern>
	</servlet-mapping>
```
访问地址是 http://host/yourWebapp/lbClassicStatus

##### 配置文件语法

Logback 配置文件的语法非常灵活。正因为灵活，所以无法用 DTD 或 XML schema 进
行定义。尽管如此，可以这样描述配置文件的基本结构：以<configuration>开头，后面有零
个或多个<appender>元素，有零个或多个<logger>元素，有最多一个<root>元素。如下图所
示：

![logbackconfig](https://ln0491.github.io/img/logbackconfig)


###### 标记名大小写敏感
从 logback 0.9.17 版起，标记名不区分大小些。比如，<logger>、<Logger>和<LOGGER>
都是合法元素且表示同一个意思。按照隐式规则，标记名除了首字母外要区分大小写。因此，
<xyz>与<Xyz>等价，但不等价于<xYz>。隐式规则一般遵循 Java 世界里常用的驼峰命名规
则。因为很难确定一个标记什么时候与显式动作相关，什么时候又与隐式动作相关，所以很
难说 XML 标记是否是大小写敏感。如果你不确定标记名的大小写，就用驼峰命名法，基本
不会错。

###### 配置logger或<logger> 元素

Logger 是用<logger>元素配置的。<logger>元素有且仅有一个 name 属性、一个可选的
level 属性和一个可选的 additivity 属性。
Level 属性的值大小写无关，其值为下面其中一个字符串：TRACE、DEBUG、INFO、
WARN、ERROR、ALL 和 OFF。还可以是一个特殊的字符串“INHERITED”或其同义词
“NULL”，表示强制继承上级的级别。
<logger>元素可以包含零个或多个<appender-ref>元素，表示这个 appender 会被添加到
该 logger。强调一下，每个用<logger>元素声明的 logger，首先会移除所有 appender，然后
才添加引用了的 appender，所以如果 logger 没有引用任何 appender，就会失去所有 appender


####### 配置根logger或<root> 元素

<root>元素配置根 logger。该元素有一个 level 属性。没有 name 属性，因为已经被命名
为“ROOT”。
Level 属性的值大小写无关，其值为下面其中一个字符串：TRACE、DEBUG、INFO、
WARN、ERROR、ALL 和 OFF。注意不能设置为“INHERITED” 或“NULL”。
<logger>元素可以包含零个或多个<appender-ref>元素。与<logger>元素类似，声明<root>
元素后，会先关闭然后移除全部当前 appender，只引用声明了的 appender。如果 root 元素没
有引用任何 appender，就会失去所有 appender

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true" scan="true" scanPeriod="30 seconds">
	<!--指定输出目的地为控制台 ch.qos.logback.core.ConsoleAppender name可以随意写 -->
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<!--指定输出规则 -->
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36}- %msg%n
			</pattern>
		</encoder>
	</appender>
	<logger name="org.springframework" level="debug" />
	<logger name="org.springframework.jdbc" level="warn" additivity="false"/>
	<!--过滤输出级别，在这个之上的才会输出  -->
	<root level="info">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

#######  配置Appender
Appender 用<appender>元素配置，该元素必要属性 name 和 class。
name 属性指定 appender 的名称，class 属性指定 appender 类的全限定名。
<appender>元素可以包含零个或多个<layout>元素、零个或多个<encoder>元素和零个或
多个<filter>元素。除了这三个常用元素之外，还可以包含 appender 类的任意数量的 javabean
属性。

<layout>元素的 class 属性是必要的，表示将被实例化的 layout 类的全限定名。和
<appender>元素一样， <layout>元素可以包含 layout 的的 javabean 属性。因为太常用了，所
以当当 layout 是 PatternLayout 时，可以省略 class 属性

<encoder>元素 class 属性是必要的，表示将被实例化的 encoder 类的全限定名。因为太
常用了，所以当当 encoder 是 PatternLayoutEncoder 时，可以省略 class 属性

记录输出到多个 appender 很简单，先定义各种 appender，然后在 logger 里进行引用，
就行了

###### 设置上下文名称

每个 logger 都关联到 logger 上下文。默认情况下，logger 上下文名为“default”。但是
你可以借助配置指令<contextName>设置成其他名字。注意一旦设置 logger 上下文名称后，
不能再改。设置上下文名称后，可以方便地区分来自不同应用程序的记录
```
<!-- 上下文建议为应用名称 -->
<contextName>logback</contextName>
```

###### 属性

```
<!--属性  -->
	<property name="log_base" value="/home/datas/logs/logback" />

	<appender name="FILE" class="ch.qos.logback.core.FileAppender">
		<file>${log_base}/myApp.log</file>
		<encoder>
			<pattern>%msg%n</pattern>
		</encoder>
	</appender>
<!--过滤输出级别，在这个之上的才会输出 -->
	<root level="info">
		<appender-ref ref="FILE" />
	</root>
```
属性引用属性文件propertites
```
<property file="src/main/java/chapters/configuration/variables1.properties" />
```

属性引用资源文件
```
<property resource="resource1.properties" />
```
文件包括
```
<include file="src/main/java/chapters/configuration/includedConfig.xml" />
```

#### Appender

##### 什么是Appender
Appender 是 负 责 写 记 录 事 件 的 组 件 。 Appender 必 须 实 现 接 口"ch.qos.logback.core.Appender"
```
package ch.qos.logback.core;
import ch.qos.logback.core.spi.ContextAware;
import ch.qos.logback.core.spi.FilterAttachable;
import ch.qos.logback.core.spi.LifeCycle;
public interface Appender<E> extends LifeCycle, ContextAware,
FilterAttachable {
public String getName();
public void setName(String name);
void doAppend(E event);
}
```
Appender 接口里的多数方法都是 getter 和 setting。值得注意的是 doAppend()方法，它唯
一的参数是类型E的对象。类型E的实际类型视 logback模块的不同而不同。在 logback-classic
模块里，E 可能是“ILoggingEvent”类型；在 logback-access 模块里，E 可能是“AccessEvent”
类型。doAppend()方法也许是 logback 框架里最重要的方法，它负责以适当的格式将记录事
件输出到合适的设备。

Appender 是被命名的实体。因为有名字，所以能被引用。Appender 接口扩展了
FilterAttachable 接口，因此 appender 实例可被关联一个或多个过滤器

Appender 是最终负责输出记录事件的组件。然而，它们可以把实际格式化的任务委托
给Layout或Encoder对象。每个layout/encoder都关联到一个且仅一个appender。有些appender
有内置的或固定的事件格式，因此它们不需要也没有 layout/encoder。例如，SocketAppender
在发送记录事件之前只是简单地对其进行序列化。

##### AppenderBase
类 ch.qos.logback.core.AppenderBase 是实现了 Appender 接口的抽象类。AppenderBase
提供所有 appender 共享的基本功能，比如设置/获取名字的方法，其活动状态和过滤器。
AppenderBase 是 loback 里所有 appender 的超类。尽管是抽象类，AppenderBase 实际上实现
了 Appender 接口的 doAppend()方法


##### OutputStreamAppender

OutputStreamAppender 把事件添加到 java.io.OutputStream。该类提供其他 appender 所需的基本服务。用户通常不直接实例化 OutputStreamAppender 对象。由于 java.io.OutputStream
一般无法被方便地映射到字符串，所以无法在配置文件里指定目标 OutputStream 对象。简
而言之，你不能在配置文件里配置 OutputStreamAppender。但这不是说 OutputStreamAppender
没有配置属性。它的属性如下。
属性名  		类型  			描述
encoder  Encoder  决定把事件写入到底层 OutputStreamAppender 的方式。

```

		<encoder>
			<pattern>%date %-5level - %msg%n</pattern>
			<charset>UTF-8</charset>
		</encoder>
```

OutputStreamAppender 是另外三个 appender 的超类：ConsoleAppender、FileAppender
及其直接子类 RollingFileAppender

![outpustreamappender.png](https://ln0491.github.io/img/outpustreamappender.png)


##### ConsoleAppender

ConsoleAppender 把事件添加到控制台，更准确地说是 System.out 或 System.err，默认为前者。ConsoleAppender 按照用户指定的 encoder 对事件进行格式化。System.out 和 System.err都是 java.io.PrintStream 类型，因此，它们被包裹在有缓冲 I/O 操作的 OutputStreamWriter 里.


* encoder  Encoder  参见 OutputStreamAppender 属性
* target  String字 符 串 “ System.out ” 或 “ System.err ”。 默 认 为“System.out”。

```
<configuration>
	<appender name="STDOUT"class="ch.qos.logback.core.ConsoleAppender">

	<encoder>
		<pattern>%-4relative [%thread] %-5level - %msg%n</pattern>
	</encoder>
	</appender>
	<root level="DEBUG">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

##### FileAppender
FileAppender 是 OutputStreamAppender 的子类，把记录事件添加到文件。目标文件通过
File 选项指定。如果文件已经存在，则根据 Append 属性追加或清空文件

* append  boolean  如果 true，事件被追加到现存文件尾部。如果 false，清空现存文件。默认为 true。
* encoder  Encoder  参见 OutputStreamAppender 属性
* File  String  被写入的文件名。如果文件不存在，则创建之。没有默认值。如果文件的父目录不存在，FileAppender 会自动创建各
级不存在的目录。
* prudent  boolean
在 prudent 模式下，FileAppender 将安全地写入指定文
件，即使其他 FileAppender 实例运行在不同 JVM 上，
比如运行在不同主机上。
prudent 默认值为 false。
prudent 模式意味着 Append 属性自动设为 true。
prudent 模式写记录事件时，大约消耗非 prudent 模式的
三倍。在一台“普通”的 PC 上，向本地硬盘写文件，
写一条记录事件，非 prudent 模式需要 10 微妙，prudent
模式需要 30 微妙。也就是非 prudent 模式每秒记录
100000 条事件，prudent 模式每秒 33000 条。
不同 JVM 写入同一个文件时，prudent 模式高效地排
列 I/O 操作。所以，由于访问文件的 JVM 的数量增加，
导致每次 I/O 操作都会有延迟。只要 I/O 操作的总数大
约是 20 次记录请求/秒，就可以忽略对性能的影响。每
秒 100 次或等多次 I/O 操作会影响性能，此时应当避免
prudent 模式。
prudent 模式可以与 RollingFileAppender 联合使用，但
有些限制。

```
<configuration>
	<appender name="FILE" class="ch.qos.logback.core.FileAppender">
		<file>testFile.log</file>
		<append>true</append>

	<encoder>
		<pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
	</encoder>
	</appender>
	<root level="DEBUG">
		<appender-ref ref="FILE" />
	</root>
</configuration>
```
##### 文件名时间戳
```
<configuration>

		<timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss" />
	<appender name="FILE" class="ch.qos.logback.core.FileAppender">

		<file>log-${bySecond}.txt</file>
		<encoder>
		<pattern>%logger{35} - %msg%n</pattern>

		</encoder>
	</appender>
	<root level="DEBUG">
		<appender-ref ref="FILE" />
	</root>
</configuration>
```
timestamp 元素有两个属性：key 和 datePattern。属性 key 是变量名，对余下的配置元素
可用。属性 datePattern 表示把当前时间（解析配置文件的时间）转换成字符串时使用的日期
模式，遵从 java.text.SimpleDateFormat 里的约定

##### RollingFileAppender

RollingFileAppender 继承 FileAppender，能够滚动记录文件。例如，RollingFileAppender
能先记录到文件“log.txt”，然后当符合某个条件时，变成记录到其他文件。
RollingFileAppender 有两个与之互动的重要子组件。第一个是 RollingPolicy，负责滚动。
第二个是 TriggeringPolicy，决定是否以及何时进行滚动。所以，RollingPolicy 负责“什么”，
TriggeringPolicy 负责“何时”。
要想 RollingFileAppender 起作用，必须同时设置 RollingPolicy 和 TriggeringPolicy。不
过，如果 RollingPolicy 也实现了 TriggeringPolicy 接口，那么只需要设置 RollingPolicy

* file  String  参加 FileAppender 属性
* append  boolean  参加 FileAppender 属性
* encoder  Encoder  参见 OutputStreamAppender 属性
* rollingPolicy  RollingPolicy  当发生滚动时，决定 RollingFileAppender 的行为。
* triggeringPolicy  TriggeringPolicy  告知 RollingFileAppender 何时激活滚动。
* prudent  boolean  prudent 模式下不支持 FixedWindowRollingPolicy。
RollingFileAppender 支 持 prudent 与TimeBasedRollingPolicy 的联合使用，但有两个限制：
1.  在 prudent 模式，不支持也不允许文件压缩（不能
在一个 JVM 压缩文件时，让另一个 JVM 写文件）。
2.  不能设置 FileAppender 的 file 属性，必须留空。实
际上，多数操作系统不允许当一个进程打开文件时
又重命名该文件

##### FixedWindowRollingPolicy
当发生滚动时，FixedWindowRollingPolicy 根据如下固定窗口（window）算法重命名文
件。
选项“fileNamePattern”代表归档（滚动）记录文件的文件名模式。该选项是必需的，
且必需在模式的某处包含标志“%i”。
下面是 FixedWindowRollingPolicy 的可用属性


* minIndex  int  窗口索引的最小值
* maxIndex  int  窗口索引的最大值
* fileNamePattern  String
例如，对于最小值和最大值分别是 1 和 3 的文件名模式
“MyLogFile%i.log”，会产生归档文件 MyLogFile1.log、
MyLogFile2.log 和 MyLogFile3.log。
该 属 性 还 可 以 指 定 文 件 压 缩 选 项 。 例 如
“MyLogFile%i.log.zip”表示归档文件必须用 zip 格式
进行压缩；还支持“.gz”格式。


```
<configuration>
	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>testFile.log</file>
	<rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
	<fileNamePattern>testFile.%i.log.zip</fileNamePattern>
	<minIndex>1</minIndex>
	<maxIndex>3</maxIndex>
	</rollingPolicy>
	<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
		<maxFileSize>5MB</maxFileSize>
	</triggeringPolicy>
	<encoder>
		<pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n	</pattern>
	</encoder>
	</appender>
	<root level="DEBUG">
		<appender-ref ref="FILE" />
	</root>
</configuration>
```

##### TimeBasedRollingPolicy
TimeBasedRollingPolicy 或许是最受流行的滚动策略。它根据时间来制定滚动策略，例
如 根 据 日 或 月 。TimeBasedRollingPolicy 既 负 责 滚 动 也 负 责 触 发 滚 动 。 实 际 上 ，
TimeBasedRollingPolicy 同时实现了 RollingPolicy 接口和 TriggeringPolicy 接口。
TimeBasedRollingPolicy 有两个属性：必需的“fileNamePattern”和可选的“maxHistory”


* fileNamePattern  String
必需。定义滚动（归档）记录文件的名字。其值应当包含文件名及“%d”格式转换符。“%d”可以包含一个
java.text.SimpleDateFormat 指定的日期时间模式。如果没有指定日期时间模式，则默认为 yyyy-MM-dd。
RollingFileAppender（TimeBasedRollingPolicy 之父）的“file”选项可有可无。
通过设置“file”属性，可以为活动文件和归档文件指定不同的位置。当前记录总是被指向由“file”属性指
定的文件。如果有“file”属性，则活动文件的名字不会改变。而如果没有“file”属性，则活动文件的名字
会根据“fileNamePattern”的值每隔一段时间就重新计算一次。下面的例子会解释这点。
“ %d{} ” 里 的 日 期 时 间 模 式 遵 循java.text.SimpleDateFormat 的约定。在 fileNamePattern
或日期时间模式里的前置“/”或后置“\”会被当作目录分隔符。
* maxHistory int

控制被保留的归档文件的最大数量，超出数量就删除旧文件。例如，假设每月滚动，且 maxHistory 是 6，则只
保留最近 6 个月的归档文件，删除之前的文件。注意当删除旧归档文件时，那些为了归档而创建的目录也会被删除。

```
<configuration>
	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<file>logFile.log</file>
	<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

<!-- keep 30 days worth of history -->
	<maxHistory>30</maxHistory>
	</rollingPolicy>
	<encoder>
		<pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
	</encoder>
	</appender>
	<root level="DEBUG">
		<appender-ref ref="FILE" />
	</root>
</configuration>
```
#### Encoder

Encoder 负责两件事，一是把事件转换为字节数组，二是把字节数组写入输出流。在
logback 0.9.19 版之前没有 encoder。在之前的版本里，多数 appender 依靠 layout 来把事件转
换成字符串并用 java.io.Writer 把字符串输出。在之前的版本里，用户需要在 FileAppender
里嵌入一个 PatternLayout。而从 0.9.19 版开始，FileAppender 和其子类使用 encoder，不接
受 layout


#### Layout
Layout 负责把事件转换成字符串。Layout 接口的 format()方法的参数是代表任何类型的
事件，返回字符串


