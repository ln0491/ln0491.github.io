---
title: mysql学习十九系统信息函数
date: 2017-02-17 13:23:58
categories: mysql
tags: mysql
---

### mysql学习十九系统信息函数

* version() 返回数据库版本号
* DATABASE() 返回当前数据库名
* USER() 返回当前用户
* LAST_INSERT_ID() 返回最近生成的AUTO_INCREMENT值


```
mysql> select version(),database(),user();
+------------+------------+----------------+
| version()  | database() | user()         |
+------------+------------+----------------+
| 5.7.17-log | company    | root@localhost |
+------------+------------+----------------+
1 row in set (0.00 sec)

```

#### 获取AUTO_INCREMENT约束的最后ID值

```
CREATE TABLE IF NOT EXISTS t_autoincrement(
id INT(11) PRIMARY KEY AUTO_INCREMENT 
);

INSERT INTO t_autoincrement VALUES(NULL);
INSERT INTO t_autoincrement VALUES(NULL);
INSERT INTO t_autoincrement VALUES(NULL);
INSERT INTO t_autoincrement VALUES(NULL);
INSERT INTO t_autoincrement VALUES(NULL);
INSERT INTO t_autoincrement VALUES(NULL);
```
```
SELECT LAST_INSERT_ID();
mysql> select last_insert_id();
+------------------+
| last_insert_id() |
+------------------+
|               6 |
+------------------+
1 row in set (0.00 sec)\
```

#### 其它的函数

* IF(value,t f) 如果value是真，返回t，否则返回f
* IFNULL(value1,value2) 如果value1不为空返回value1，否则返回value2
* CASE WHEN [value1] THEN[result1]…ELSE[default] END 如果value1是真，返回result1，否则返回default
* CASE [expr]WHEN[value1] THEN[result1]…ELSE[default] END 如果expr等于value1，返回result1，否则返回default
* PASSWORD(str)  实现对字符串str进行加密
* FORMAT(x,n) 实现将数字x进行格式化，保留n位小数
* INET_ATON(ip) 实现将IP地址转换成数字
* INET_NTOA(x) 实现将数字转换成IP
* GET_LOCT(name,time) 创建一个持续时间为time的名为name的锁
* RELEASE_LOCT(name) 为名为name的锁进行解锁
* BENCHMARK(count,expr) 实现将表达式重复执行count次
* CONVERT(s USING cs) 实现将字符串s的字符集变成cs
* CONV(N,from_base,to_base)函数




