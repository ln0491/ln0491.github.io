---
title: spring-boot入门三使用xml配置
date: 2017-03-01 14:28:39
categories: spring-boot
tags: spring-boot
---

### spring-boot入门三使用xml配置
Spring-boot提倡0配置，但有时候需要XML之类的配置，这里需要导入

```
@ImportResource(value={"classpath:some.xml","classpath:/config/test.xml"})
```