---
title: mysql学习六索引基本操作
date: 2017-02-13 10:41:00
categories: mysql
tags: mysql
---

## mysql学习六索引基本操作

### 索引
索引就像书的目录一样。主要是为了提高从表中检索数据的速度。

### 以下情况适合创建索引

* 经常被查询的字段，即在WHERE子句中出现的字段
* 在分组的字段，即在GROUG BY子句中出现的字段
* 存在依赖关系的子表与父表之间的联合查询，即主键或者外键字段
* 设置唯一完整性约束字段

### 以下情况不适合创建索引

* 在查询中很少被使用的字段
* 拥有许多重复值的字段

### 创建和查看普通索引

```
CREATE TABLE 表名(
	属性名 数据类型，
	属性名 数据类型，
	属性名 数据类型，
	
	INDEX | KEY 【索引名】（属性名1，【长度】【ASC|DESC】）
);
```
INDEX或者KEY参数用来指定字段为索引，
索引名参数用来指定所创建的索引名， 
属性名1，用来指定索引所关联的字段名称，
长度用来指定索引长度，
ASC参数用来指定索引为升序，DESC用来指定索引为降序

```
mysql> drop table t_employee;
Query OK, 0 rows affected (0.21 sec)

mysql> drop table t_dept;
Query OK, 0 rows affected (0.10 sec)

mysql> drop database company;
Query OK, 0 rows affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| databasetest       |
| mysql              |
| performance_schema |
| sakila             |
| sys                |
| world              |
+--------------------+
7 rows in set (0.00 sec)

mysql> create database company;
Query OK, 1 row affected (0.00 sec)

mysql> use company;
Database changed
mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT ,
    -> dname VARCHAR(20)  ,
    -> loc   VARCHAR(40) ,
    -> INDEX index_deptno (deptno)
    -> );
Query OK, 0 rows affected (0.30 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  | MUL | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> show create table t_dept;
+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table  | Create Table                                                                                                                                                                                        |
+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| t_dept | CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL,
  KEY `index_deptno` (`deptno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> show create table t_dept \G
*************************** 1. row ***************************
       Table: t_dept
Create Table: CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL,
  KEY `index_deptno` (`deptno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

```

show create table t_dept \G 查看是否创建索引

### 检验索引是否被使用
EXPLAIN select * from t_dept where deptno=1;
```
mysql> EXPLAIN select * from t_dept where deptno=1;
+----+-------------+--------+------------+------+---------------+--------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys | key          | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+---------------+--------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | t_dept | NULL       | ref  | index_deptno  | index_deptno | 5       | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+---------------+--------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
```
possible_keys | key
字段处的值为报建的索引名，说这个索引已经存在，而且开始使用

### 在已经存在的表上创建普通索引


CREATE INDEX 索引名 on 表名（属性名【(长度)】【ASC|DESC】）
删除表，创建一个没有索引的空表
```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.17 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT ,
    -> dname VARCHAR(20)  ,
    -> loc   VARCHAR(40)
    -> );
Query OK, 0 rows affected (0.19 sec)

mysql> show tables;
+-------------------+
| Tables_in_company |
+-------------------+
| t_dept            |
+-------------------+
1 row in set (0.00 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> explain select * from t_dept where deptno=1;
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    1 |   100.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```

下面是创建索引

```
mysql> create index index_deptno on t_dept(deptno);
Query OK, 0 rows affected (0.24 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> explain select * from t_dept where deptno=1;
+----+-------------+--------+------------+------+---------------+--------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys | key          | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+---------------+--------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | t_dept | NULL       | ref  | index_deptno  | index_deptno | 5       | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+---------------+--------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

ysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  | MUL | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> show create table t_dept;
+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table  | Create Table                                                                                                                                                                                        |
+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| t_dept | CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL,
  KEY `index_deptno` (`deptno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+--------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
Key已经有值，说明创建成功

### 在已经存在的表上添加普通索引
ALTER table 表名 ADD INDEX 索引名 （属性名【(长度)】【ASC|DESC】）

准备数据
```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.18 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT ,
    -> dname VARCHAR(20)  ,
    -> loc   VARCHAR(40)
    -> );
Query OK, 0 rows affected (0.21 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> show create table t_dept \G
*************************** 1. row ***************************
       Table: t_dept
Create Table: CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```

添加索引

```
mysql> alter table t_dept add index index_deptno (deptno);
Query OK, 0 rows affected (0.20 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  | MUL | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> show create table t_dept \G
*************************** 1. row ***************************
       Table: t_dept
Create Table: CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL,
  KEY `index_deptno` (`deptno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```

KEY已经有值

### 创建来查看唯一索引

```
CREATE TABLE 表名(
	属性名 数据类型，
	属性名 数据类型，
	属性名 数据类型，
	
	UNIQUE INDEX | KEY 【索引名】（属性名1，【长度】【ASC|DESC】）
);
```
创建表时直接创建唯一索引
```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.45 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT ,
    -> dname VARCHAR(20)  ,
    -> loc   VARCHAR(40),
    -> UNIQUE INDEX index_deptno (deptno)
    -> );
Query OK, 0 rows affected (0.20 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  | UNI | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> show create table t_dept \G
*************************** 1. row ***************************
       Table: t_dept
Create Table: CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL,
  UNIQUE KEY `index_deptno` (`deptno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```
KEY的值为UNI,表示创建成功

###  在已经存在的表上创建唯一索引
CREATE UNIQUE INDEX 索引名 on 表名（属性名【(长度)】【ASC|DESC】）

```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.16 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT UNIQUE,
    -> dname VARCHAR(20)  ,
    -> loc   VARCHAR(40)
    ->
    -> );
Query OK, 0 rows affected (0.24 sec)

mysql> create unique index index_deptno on t_dept (deptno);
Query OK, 0 rows affected, 1 warning (1.30 sec)
Records: 0  Duplicates: 0  Warnings: 1

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  | UNI | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> show create table t_dept;
+--------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table  | Create Table                                                                                                                                                                                                                                 |
+--------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| t_dept | CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL,
  UNIQUE KEY `deptno` (`deptno`),
  UNIQUE KEY `index_deptno` (`deptno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+--------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

```
KEY的值为UNI,表示创建成功

###  在已经存在的表上添加唯一索引

ALTER table 表名 ADD UNIQUE INDEX 索引名 （属性名【(长度)】【ASC|DESC】）

```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.21 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT UNIQUE,
    -> dname VARCHAR(20)  ,
    -> loc   VARCHAR(40)
    ->
    -> );
Query OK, 0 rows affected (0.21 sec)

mysql> alter table t_dept add unique index index_deptno (deptno);
Query OK, 0 rows affected, 1 warning (0.20 sec)
Records: 0  Duplicates: 0  Warnings: 1

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  | UNI | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> explain  select * from t_dept where deptno=1;
+----+-------------+--------+------------+-------+---------------------+--------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type  | possible_keys       | key    | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+-------+---------------------+--------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | t_dept | NULL       | const | deptno,index_deptno | deptno | 5       | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+-------+---------------------+--------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> explain  select * from t_dept where deptno=1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t_dept
   partitions: NULL
         type: const
possible_keys: deptno,index_deptno
          key: deptno
      key_len: 5
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.00 sec)
```

### 创建和查看全文索引
全文索引主要关联在数据类型为 CHAR ,VARCHAR和TEXT的字段上,
Mysql从3.23.23版本开始支持全文索引，只能在存储引擎为MyISAM的数据表表创建全文索引，默认情况下不区分大小写
```
CREATE TABLE 表名(
	属性名 数据类型，
	属性名 数据类型，
	属性名 数据类型，
	
	FULLTEXT INDEX | KEY 【索引名】（属性名1，【长度】【ASC|DESC】）
);
```
```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE `t_dept` (
    ->   `deptno` INT(11) DEFAULT NULL,
    ->   `dname` VARCHAR(20) DEFAULT NULL,
    ->   `loc` VARCHAR(40) DEFAULT NULL
    ->
    -> ) ENGINE=MYISAM DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.04 sec)

mysql> create fulltext index index_loc on t_dept(loc);
Query OK, 2 rows affected (0.05 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> explain select * from t_dept where loc='bj';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | index_loc     | NULL | NULL    | NULL |    2 |    50.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from t_dept where loc='sz';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | index_loc     | NULL | NULL    | NULL |    2 |    50.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql>
```
###  在已经存在的表上创建全文索引
CREATE FULLTEXT INDEX 索引名 on 表名（属性名【(长度)】【ASC|DESC】）

创建全文索引

```
DROP TABLE IF EXISTS `t_dept`;

CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL,
  FULLTEXT KEY `index_loc` (`loc`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

mysql> explain select * from t_dept where loc='cj';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | index_loc     | NULL | NULL    | NULL |    2 |    50.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```

###  在已经存在的表上添加全文索引

ALTER table 表名 ADD FULLTEXT INDEX 索引名 （属性名【(长度)】【ASC|DESC】）

```
mysql> CREATE TABLE `t_dept` (
    ->   `deptno` INT(11) ,
    ->   `dname` VARCHAR(20) ,
    ->   `loc` VARCHAR(40)
    ->
    -> ) ENGINE=MYISAM DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.04 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> alter table t_dept add fulltext index index_loc(loc);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  | MUL | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> explain select * from t_dept where loc='sz';
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+--------------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                          |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+--------------------------------+
|  1 | SIMPLE      | NULL  | NULL       | NULL | NULL          | NULL | NULL    | NULL | NULL |     NULL | no matching row in const table |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+--------------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from t_dept where loc='sz';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | index_loc     | NULL | NULL    | NULL |    2 |    50.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  | MUL | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```

### 创建和查看多列索引
```
CREATE TABLE 表名(
	属性名 数据类型，
	属性名 数据类型，
	属性名 数据类型，
	
	 INDEX | KEY 【索引名】（属性名1，【长度】【ASC|DESC】,
                         （属性名2，【长度】【ASC|DESC】
							.....)
	）
);
```
多列索引，是指在创建索引时，所关联的字段不是一个字段，而是多字段，虽然可以通过所关联的字段进行查询，但是只有查询条件中使用了所关联字段中的第一个字段，多列索引才会被使用



```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE `t_dept` (
    ->   `deptno` INT(11) ,
    ->   `dname` VARCHAR(20) ,
    ->   `loc` VARCHAR(40),
    ->   INDEX index_dname_loc(dname,loc)
    ->
    -> );
Query OK, 0 rows affected (0.18 sec)

mysql> insert into t_dept values(1,'sz','szn'),(2,'sh','shg');
Query OK, 2 rows affected (0.05 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  | MUL | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> explain select * from t_dept where loc='szn';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    2 |    50.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from t_dept where dname='sz';
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys   | key             | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | t_dept | NULL       | ref  | index_dname_loc | index_dname_loc | 63      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
```


###  在已经存在的表上创建多列索引

CREATE  INDEX 索引名 on 表名（属性名1，【长度】【ASC|DESC】,
（属性名2，【长度】【ASC|DESC】
.....)


```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.15 sec)

mysql>
mysql> CREATE TABLE `t_dept` (
    ->   `deptno` INT(11) ,
    ->   `dname` VARCHAR(20) ,
    ->   `loc` VARCHAR(40)
    ->
    ->
    -> );
Query OK, 0 rows affected (0.31 sec)

mysql> INSERT INTO t_dept VALUES(1,'sz','szn'),(2,'sh','shg');
Query OK, 2 rows affected (0.05 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> create index index_dname_loc on t_dept (dname,loc);
Query OK, 0 rows affected (0.25 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  | MUL | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.01 sec)

mysql> explain select * from t_dept where loc='shg';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    2 |    50.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from t_dept where dname='sh';
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys   | key             | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | t_dept | NULL       | ref  | index_dname_loc | index_dname_loc | 63      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
```

###  在已经存在的表上添加多列索引

ALTER table 表名 
ADD  INDEX 索引名 
（属性名1，【长度】【ASC|DESC】,
（属性名2，【长度】【ASC|DESC】
.....)

```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.15 sec)

mysql>
mysql> CREATE TABLE `t_dept` (
    ->   `deptno` INT(11) ,
    ->   `dname` VARCHAR(20) ,
    ->   `loc` VARCHAR(40)
    ->
    ->
    -> );
Query OK, 0 rows affected (0.31 sec)

mysql> INSERT INTO t_dept VALUES(1,'sz','szn'),(2,'sh','shg');
Query OK, 2 rows affected (0.05 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
mysql> alter table t_dept add index index_loc_dname (loc,dname);
Query OK, 0 rows affected (0.23 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  | MUL | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> explain select * from t_dept where loc='szn';
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys   | key             | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | t_dept | NULL       | ref  | index_loc_dname | index_loc_dname | 123     | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from t_dept where dname='sz';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t_dept | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    2 |    50.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```

### 删除索引

DROP INDEX 索引名 on 表名

```
mysql> drop index  index_dname_loc on t_dept;
Query OK, 0 rows affected (0.14 sec)
Records: 0  Duplicates: 0  Warnings: 0
```