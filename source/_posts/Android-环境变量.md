---
title: Android 环境变量
date: 2016-09-28 13:57:33
categories: android
tags: android
---

## JDK环境变量配置

* JAVA_HOME
     
         JDK的安装路径C:\Program Files (x86)\Java\jdk1.8.0_25 

* CLASSPATH
    
         .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar

* PATH

         %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin

## Android SDK环境变量

* ANDROID_HOME
        
        D:\Android\sdk

* PATH

         %ANDROID_HOME%\platform-tools;%ANDROID_HOME%\tools;

## MAVEN 环境变量

* MAVEN_HOME

         D:\JAVA\apache-maven-3.3.9

* PATH
* 
         %MAVEN_HOME%\bin;

MAVEN_OPTS
    
        -Xms128m -Xmx512m

* 查看是否配置成功
> mvn -v

* 默认本地仓库
> 系统当前用户目录下.m2下,Maven缺省的本地仓库路径为${user.home}/.m2/repository。 

* 修改本地创库

>在D盘下新m2\repository文件夹    可以通过修改${user.home}/.m2/settings.xml配置本地仓库路径     修改Maven安装目录下的 conf/文件夹内的setting.xml文件，新增一行：

 
     <localRepository>D:/JAVA/m2/repository</localRepository>
>（表示本地仓库的地址为：D:/JAVA/m2/repository）

将setting.xml复制一份到D:/JAVA/m2/repository

      注意ManvenConfig下有此文件，便不要修改，复制出来修改