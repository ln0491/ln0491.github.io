---
title: spring-boot入门二配置文件
date: 2017-03-01 10:08:35
categories: spring-boot
tags: spring-boot
---

### spring-boot入门二配置文件


#### 配置文件

Spring Boot使用了一个全局的配置文件application.properties，放在src/main/resources目录下或者类路径的/config下。Sping Boot的全局配置文件的作用是对一些默认配置的配置值进行修改。

application.properties和application.yml文件可以放在一下四个位置：
* 外置，在相对于应用程序运行目录的/congfig子目录里。
* 外置，在应用程序运行的目录里
* 内置，在config包内
* 内置，在Classpath根目录
同样，这个列表按照优先级排序，也就是说，src/main/resources/config下application.properties覆盖src/main/resources下application.properties中相同的属性

此外，如果你在相同优先级位置同时有application.properties和application.yml，那么application.yml里面的属性就会覆盖application.properties里的属性

#### Profile-多环境配置

当应用程序需要部署到不同运行环境时，一些配置细节通常会有所不同，最简单的比如日志，生产日志会将日志级别设置为WARN或更高级别，并将日志写入日志文件，而开发的时候需要日志级别为DEBUG，日志输出到控制台即可。
如果按照以前的做法，就是每次发布的时候替换掉配置文件，这样太麻烦了，Spring Boot的Profile就给我们提供了解决方案，命令带上参数就搞定。
这里我们来模拟一下，只是简单的修改端口来测试
在Spring Boot中多环境配置文件名需要满足application-{profile}.properties的格式，其中{profile}对应你的环境标识，比如：
application-dev.properties：开发环境
application-prod.properties：生产环境
想要使用对应的环境，只需要在application.properties中使用spring.profiles.active属性来设置，值对应上面提到的{profile}，这里就是指dev、prod这2个。
当然你也可以用命令行启动的时候带上参数：
```
java -jar xxx.jar --spring.profiles.active=dev

```
我给不同的环境添加不同的端口属性server.port，然后根据指定不同的spring.profiles.active来切换使用。各位可以自己试试。这里就不贴代码了。
除了可以用profile的配置文件来分区配置我们的环境变量，在代码里，我们还可以直接用@Profile注解来进行配置，例如数据库配置，这里我们先定义一个接口
```
public  interface DBConnector { public  void  configure(); }

```
分别定义俩个实现类来实现它
```
/**
  * 测试数据库
  */
@Component
@Profile("testdb")
public class TestDBConnector implements DBConnector {
    @Override
    public void configure() {
        System.out.println("testdb");
    }
}
/**
 * 生产数据库
 */
@Component
@Profile("devdb")
public class DevDBConnector implements DBConnector {
    @Override
    public void configure() {
        System.out.println("devdb");
    }
}
```
通过在配置文件激活具体使用哪个实现类
```
spring.profiles.active=testdb

```
```
@RestController
@RequestMapping("/task")
public class TaskController {

    @Autowired DBConnector connector ;

    @RequestMapping(value = {"/",""})
    public String hellTask(){

        connector.configure(); //最终打印testdb     
        return "hello task !! myage is " + myage;
    }
}
```
除了spring.profiles.active来激活一个或者多个profile之外，还可以用spring.profiles.include来叠加profile
```

spring.profiles.active: testdb  
spring.profiles.include: proddb,prodmq

```
#### 配置上下文
在resources目录下建立application.properties文件
```
server.context-path=/bt
```
访问
http://127.0.0.1:8080/bt/hello

