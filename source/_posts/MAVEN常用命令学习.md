---
title: MAVEN常用命令学习
date: 2016-09-28 13:49:57
categories: java
tags: maven
---
## MAVEN学习

## 清理target目录下的所有文件

    mvn clean

## 项目构建与编译

    mvn clean compile
>在targe目录下生成文件

## 构建测试

    mvn clean test
>在targe目录下生成文件,在执行test之前会先执行compile

## 打包

    mvn clean package
>在targe目录下生成jar,执行package之前会先执行test

## 安装jar包到本地仓库

    mvn clean install
>执行install之前会先执行，package

## 下载源码

    mvn dependency:sources

## 关联源码

    mvn eclipse:eclipse 

## 在Eclipce中配置

![mavensource.png](https://ln0491.github.io/img/maven.png)