---
title: 日志组件slf4j介绍及配置详解
date: 2017-01-03 13:31:19
categories: log
tags: log
---

## maven依赖

```
<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>1.7.21</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.21</version>
		</dependency>
```

## 最基本的配置

log4j.properties

```
#定义输出级别和输出平台,控件台输出
log4j.rootLogger=debug, stdout
#设定stdout输出平台 org.apache.log4j.ConsoleAppender（控制台）
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
```

## 示例

```
import java.util.Date;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Test1 {
	
	protected static Logger logger = LoggerFactory.getLogger(Test1.class);

	public static void main(String[] args) {

		logger.debug("这是个测试时间{}"+new Date());
		
	}

}
```
结果
```
这是个测试时间{}Tue Jan 03 13:50:39 CST 2017
```

##  输出级别的种类

* ERROR 为严重错误 主要是程序的错误
* WARN 为一般警告，比如session丢失
* INFO 为一般要显示的信息，比如登录登出
* DEBUG 为程序的调试信息

## 配置日志信息输出目的地 log4j.appender.appenderName=??

* org.apache.log4j.ConsoleAppender（控制台）
* org.apache.log4j.FileAppender（文件）
* org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
* org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
* org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）


## 配置日志信息的格式 log4j.appender.appenderName.layout = ??

* org.apache.log4j.HTMLLayout（以HTML表格形式布局）
* org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
* org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）
* org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）


## ConsoleAppender选项

* Threshold=DEBUG:指定日志消息的输出最低层次。 
* ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 
* Target=System.err:默认情况下是System.out,指定输出控制台

```
#定义输出级别和输出平台,控件台输出
log4j.rootLogger=debug, stdout
#设定stdout输出平台 org.apache.log4j.ConsoleAppender（控制台）
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
#指定输出的最低级别
log4j.appender.stdout.Threshold=debug
# 指定默认值是true,意谓着所有的消息都会被立即输出。 fasle 不输出
log4j.appender.stdout.ImmediateFlush=true
#默认情况下是System.out,指定输出控制台-err为红色的提示
log4j.appender.stdout.Target=system.err
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
```

## FileAppender 选项

* Threshold=DEBUG:指定日志消息的输出最低层次。 
* ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 
* File=mylog.txt:指定消息输出到mylog.txt文件。 
* Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。


## RollingFileAppender 选项

* Threshold=DEBUG:指定日志消息的输出最低层次。 
* ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 
* File=mylog.txt:指定消息输出到mylog.txt文件。 
* Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。 
* MaxFileSize=100KB: 后缀可以是KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动，即将原来的内容移到mylog.log.1文件。 
* MaxBackupIndex=2:指定可以产生的滚动文件的最大数。

## 日志信息格式中几个符号所代表的含义 ConversionPattern
og4j.appender.appenderName.layout.ConversionPattern格式化日志信息，Log4J采用类似C语言中的printf函数的打印格式格式化日志信息，打印参数如下：

* -X号: X信息输出时左对齐； 
* %p: 输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL, 
* %d: 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss,SSS}，输出类似：2002年10月18日 22：10：28，921 
* %r: 输出自应用启动到输出该log信息耗费的毫秒数 
* %c: 输出日志信息所属的类目，通常就是所在类的全名 
* %t: 输出产生该日志事件的线程名 
* %l: 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main (TestLog4.java:10) 
* %x: 输出和当前线程相关联的NDC(嵌套诊断环境),尤其用到像Java servlets这样的多客户多线程的应用中。 
* %%: 输出一个”%”字符 
* %F: 输出日志消息产生时所在的文件名称 
* %L: 输出代码中的行号 
* %m: 输出代码中指定的消息,产生的日志具体信息 
* %n: 输出一个回车换行符，Windows平台为”\r\n”，Unix平台为”\n”输出日志信息换行

因为日志级别分别有error,warn,info,debug,fatal5种，有些是5个字母的，有些是4个字母的，
如果直接
写%p就会对不齐，
![p.png](https://ln0491.github.io/img/p.png)
%-5p的意思是日志级别输出左对齐，右边以空格填充，
![-5p.png](https://ln0491.github.io/img/-5p.png)
%5p的意思是日志级别输出右对齐，左边以空格填充，
![5p.png](https://ln0491.github.io/img/5p.png)

## 控件台配置
```
#定义输出级别和输出平台,控件台输出
log4j.rootLogger=debug, stdout
#设定stdout输出平台 org.apache.log4j.ConsoleAppender（控制台）
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
#指定输出的最低级别
log4j.appender.stdout.Threshold=debug
# 指定默认值是true,意谓着所有的消息都会被立即输出。 fasle 不输出
log4j.appender.stdout.ImmediateFlush=true
#默认情况下是System.out,指定输出控制台-err为红色的提示
log4j.appender.stdout.Target=system.err
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
#指定输入的格式对输出的内容进行格式化
#%p  输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL
#%d 输出日志时间点的日期或时间，
#%l 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数
#%m 输出代码中指定的消息,产生的日志具体信息 
#%n 输出一个回车换行符，Windows平台为”\r\n”，Unix平台为”\n”输出日志信息换行
log4j.appender.stdout.layout.ConversionPattern=[%-5p][%d{yyyy-MM-dd HH:mm:ss}][%l]%m%n
```
## 一份完整
```
#定义输出级别和输出平台,控件台输出 appender(目的地)可以有多个
log4j.rootLogger=debug, stdout,FILE,errorLog
#设定stdout输出平台 org.apache.log4j.ConsoleAppender（控制台）
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
#指定输出的最低级别
log4j.appender.stdout.Threshold=debug
# 指定默认值是true,意谓着所有的消息都会被立即输出。 fasle 不输出
log4j.appender.stdout.ImmediateFlush=true
#默认情况下是System.out,指定输出控制台-err为红色的提示
log4j.appender.stdout.Target=system.err
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
#指定输入的格式对输出的内容进行格式化
#%p  输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL
#%d 输出日志时间点的日期或时间，
#%l 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数
#%m 输出代码中指定的消息,产生的日志具体信息 
#%n 输出一个回车换行符，Windows平台为”\r\n”，Unix平台为”\n”输出日志信息换行
log4j.appender.stdout.layout.ConversionPattern=[%-5p][%d{yyyy-MM-dd HH:mm:ss}][%l]%m%n

##配置输出到文件-每天一个文件

log4j.appender.FILE=org.apache.log4j.DailyRollingFileAppender

#指定输出的最低级别
log4j.appender.FILE.Threshold=debug
# 指定默认值是true,意谓着所有的消息都会被立即输出。 fasle 不输出
log4j.appender.FILE.ImmediateFlush=true
#默认情况下是System.out,指定输出控制台-err为红色的提示
log4j.appender.FILE.File= logs/logs.log
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
log4j.appender.FILE.layout=org.apache.log4j.PatternLayout
#指定输入的格式对输出的内容进行格式化
#%p  输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL
#%d 输出日志时间点的日期或时间，
#%l 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数
#%m 输出代码中指定的消息,产生的日志具体信息 
#%n 输出一个回车换行符，Windows平台为”\r\n”，Unix平台为”\n”输出日志信息换行
log4j.appender.FILE.layout.ConversionPattern=[%-5p][%d{yyyy-MM-dd HH:mm:ss}][%l]%m%n


## 输入文件指定大小与上面每天一个文件2选一errorLog/FILE
log4j.appender.errorLog=org.apache.log4j.RollingFileAppender

#指定输出的最低级别-保存日志取tomcat下logs目录
log4j.appender.errorLog.Threshold=debug
# 指定默认值是true,意谓着所有的消息都会被立即输出。 fasle 不输出
log4j.appender.errorLog.ImmediateFlush=true
#默认情况下是System.out,指定输出控制台-err为红色的提示
log4j.appender.errorLog.File= ${catalina.base}/logs.log
#指定文件的大小
log4j.appender.errorLog.MaxFileSize= 20480KB
# 指定最大文件个数
log4j.appender.errorLog.MaxBackupIndex= 10
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
log4j.appender.errorLog.layout=org.apache.log4j.PatternLayout  
#指定输入的格式对输出的内容进行格式化
#%p  输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL
#%d 输出日志时间点的日期或时间，
#%l 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数
#%m 输出代码中指定的消息,产生的日志具体信息 
#%n 输出一个回车换行符，Windows平台为”\r\n”，Unix平台为”\n”输出日志信息换行
log4j.appender.errorLog.layout.ConversionPattern=[%-5p][%d{yyyy-MM-dd HH:mm:ss}][%l]%m%n
```