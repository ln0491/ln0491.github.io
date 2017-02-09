---
title: mysql学习一
date: 2017-02-09 11:31:01
categories: mysql
tags: mysql
---
## mysql学习一

### 查看数据库

```
show databases;
```
### 创建数据库
create DATABASE  数据库名称
```
create DATABASE databasetest;

```
### 选择数据库
use  数据库名称
```
use databasetest;
------------
Database changed;切换成功
```

### 查看当前数据库名称

```
SELECT DATABASE();

```

### 删除数据库
drop DATABASE  数据库名称
```
drop DATABASE databasetest;

```

### 数据库状态

```
status;

--------------
mysql  Ver 14.14 Distrib 5.7.17, for Win64 (x86_64)

Connection id:          5
Current database:
Current user:           root@localhost
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.7.17-log MySQL Community Server (GPL)
Protocol version:       10
Connection:             127.0.0.1 via TCP/IP
Server characterset:    utf8
Db     characterset:    utf8
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 hours 25 min 34 sec

Threads: 2  Questions: 57  Slow queries: 0  Opens: 114  Flush tables: 1  Open tables: 107  Queries per second avg: 0.006
--------------
```




