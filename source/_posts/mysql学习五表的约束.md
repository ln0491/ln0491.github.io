---
title: mysql学习五表的约束
date: 2017-02-10 13:43:33
categories: mysql
tags: mysql
---


## mysql学习五表的约束

![mysql.png](https://ln0491.github.io/img/mysql.png)

```
mysql> show tables;
+-------------------+
| Tables_in_company |
+-------------------+
| tab_dept          |
+-------------------+
1 row in set (0.00 sec)

mysql> drop table tab_dept;
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
```

### 添加非空约束 NOT NULL

```
mysql> create database company;
Query OK, 1 row affected (0.00 sec)

mysql> use company;
Database changed
mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT(20) NOT NULL,
    -> dname VARCHAR(20),
    -> loc   VARCHAR(40)
    -> );
Query OK, 0 rows affected (0.18 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(20)     | NO   |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```

插入NULL数据时会报错
```
mysql> insert into t_dept values(null,'RD','SZ');
ERROR 1048 (23000): Column 'deptno' cannot be null
mysql> insert into t_dept values(1001,'RD','SZ');
Query OK, 1 row affected (0.02 sec)

mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|   1001 | RD    | SZ   |
+--------+-------+------+
1 row in set (0.00 sec)
```
### 添加默认值

```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.15 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT(20) NOT NULL,
    -> dname VARCHAR(20) DEFAULT '测试默认值',
    -> loc   VARCHAR(40)
    -> );
Query OK, 0 rows affected (0.20 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+-----------------+-------+
| Field  | Type        | Null | Key | Default         | Extra |
+--------+-------------+------+-----+-----------------+-------+
| deptno | int(20)     | NO   |     | NULL            |       |
| dname  | varchar(20) | YES  |     | 测试默认值      |       |
| loc    | varchar(40) | YES  |     | NULL            |       |
+--------+-------------+------+-----+-----------------+-------+
3 rows in set (0.00 sec)

mysql> insert into t_dept (deptno,loc) values(1001,'QQ');
Query OK, 1 row affected (0.04 sec)

mysql> select * from t_dept;
+--------+-----------------+------+
| deptno | dname           | loc  |
+--------+-----------------+------+
|   1001 | 测试默认值      | QQ   |
+--------+-----------------+------+
1 row in set (0.00 sec)
```
插入值时没有写时，会使用默认值

### 添加唯一约束

```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.13 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT(20) NOT NULL,
    -> dname VARCHAR(20) UNIQUE, #唯一
    -> loc   VARCHAR(40)
    -> );
Query OK, 0 rows affected (0.18 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(20)     | NO   |     | NULL    |       |
| dname  | varchar(20) | YES  | UNI | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> insert into t_dept (deptno,danme,loc) values(1001,'RD','QQ');
ERROR 1054 (42S22): Unknown column 'danme' in 'field list'
mysql> insert into t_dept (deptno,danme,loc) values(1001,'RD','QQ');
ERROR 1054 (42S22): Unknown column 'danme' in 'field list'
mysql> insert into t_dept (deptno,dname,loc) values(1001,'RD','QQ');
Query OK, 1 row affected (0.03 sec)

mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|   1001 | RD    | QQ   |
+--------+-------+------+
1 row in set (0.00 sec)

mysql> insert into t_dept (deptno,dname,loc) values(1002,'TEST','ALI');
Query OK, 1 row affected (0.03 sec)

mysql> insert into t_dept (deptno,dname,loc) values(1003,'TEST','WWA');
ERROR 1062 (23000): Duplicate entry 'TEST' for key 'dname'
mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|   1001 | RD    | QQ   |
|   1002 | TEST  | ALI  |
+--------+-------+------+
2 rows in set (0.00 sec)
```
dname 字段不能重复，为唯一约束，添加相同时会报错
```
ERROR 1062 (23000): Duplicate entry 'TEST' for key 'dname'
```
另外一种添加唯一约束

```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.13 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT(20) NOT NULL,
    -> dname VARCHAR(20),
    -> loc   VARCHAR(40),
    -> CONSTRAINT uk_dname UNIQUE (dname)  #创建唯一约束名称
    -> );
Query OK, 0 rows affected (0.31 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(20)     | NO   |     | NULL    |       |
| dname  | varchar(20) | YES  | UNI | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```

### 设置主键 PK primary key

当想使用数据库表中的某个字段来唯一标识所有记录时，可以使用PK来进行约束设置
PK约束就是在创建数据库表时为某个字段加个 primary key约束条件

```
mysql> drop table t_dept;
Query OK, 0 rows affected (0.13 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT PRIMARY KEY,
    -> dname VARCHAR(20),
    -> loc   VARCHAR(40)
    -> );
Query OK, 0 rows affected (0.18 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | NO   | PRI | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```
加个primary key约束的会在key上显示pri表示主键约束，主键不能重复
```
mysql> insert into t_dept (deptno,dname,loc) values(1003,'TEST','WWA');
Query OK, 1 row affected (0.03 sec)

mysql> insert into t_dept (deptno,dname,loc) values(1003,'RD','SZ');
ERROR 1062 (23000): Duplicate entry '1003' for key 'PRIMARY'
mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|   1003 | TEST  | WWA  |
+--------+-------+------+
1 row in set (0.00 sec)
```
报错表示 
ERROR 1062 (23000): Duplicate entry '1003' for key 'PRIMARY'

另外一种写法

```
mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT,
    -> dname VARCHAR(20),
    -> loc   VARCHAR(40),
    -> CONSTRAINT pk_deptpno PRIMARY KEY (deptno)
    -> );
Query OK, 0 rows affected (0.30 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | NO   | PRI | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.01 sec)
```
效果是一样的

### 多字段组合主键
CONSTRAINT 约束名 PRIMARY KEY (属性名1,属性名2...)

```
mysql> drop tables t_dept;
Query OK, 0 rows affected (0.12 sec)

mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT,
    -> dname VARCHAR(20),
    -> loc   VARCHAR(40),
    -> CONSTRAINT pk_deptpno_dname PRIMARY KEY (deptno,dname)
    -> );
Query OK, 0 rows affected (0.19 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | NO   | PRI | NULL    |       |
| dname  | varchar(20) | NO   | PRI | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```

插入数据
```
mysql> insert into t_dept value(1001,'RD','SZ');
Query OK, 1 row affected (0.05 sec)

mysql> insert into t_dept value(1001,'RD','SZ');
ERROR 1062 (23000): Duplicate entry '1001-RD' for key 'PRIMARY'
mysql> insert into t_dept value(1002,'RD','SZ');
Query OK, 1 row affected (0.03 sec)

mysql> insert into t_dept value(1002,'RD','SZ');
ERROR 1062 (23000): Duplicate entry '1002-RD' for key 'PRIMARY'
mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|   1001 | RD    | SZ   |
|   1002 | RD    | SZ   |
+--------+-------+------+
2 rows in set (0.00 sec)

mysql> insert into t_dept value(1002,'RD2','SZ');
Query OK, 1 row affected (0.05 sec)

mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|   1001 | RD    | SZ   |
|   1002 | RD    | SZ   |
|   1002 | RD2   | SZ   |
+--------+-------+------+
3 rows in set (0.00 sec)
```
发现在我个主键时，只要有一个不重复就可以插入数据

### 设置字段自增（AUTO_INCREMENT)

一个表只能有一个字段使用AUTO_INCREMENT来约束，该字段数据类型必须为整数类型，由于设置AUTO_INCREMENT后的字段会生成唯一约束ID，所以该字段也经常会设置为PK主键

属性名 数据类型（整数） PRIMARY KEY AUTO_INCREMENT,

```
mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT PRIMARY KEY AUTO_INCREMENT, # 主键加自增
    -> dname VARCHAR(20) UNIQUE NOT NULL,# 唯一
    -> loc   VARCHAR(40) NOT NULL  #非空
    ->
    -> );
Query OK, 0 rows affected (0.22 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+----------------+
| Field  | Type        | Null | Key | Default | Extra          |
+--------+-------------+------+-----+---------+----------------+
| deptno | int(11)     | NO   | PRI | NULL    | auto_increment |
| dname  | varchar(20) | NO   | UNI | NULL    |                |
| loc    | varchar(40) | NO   |     | NULL    |                |
+--------+-------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)
```

插入数据

```
mysql> insert into t_dept(dname,loc) values('RD','SZ');
Query OK, 1 row affected (0.06 sec)

mysql> insert into t_dept(dname,loc) values('TEST','BJ');
Query OK, 1 row affected (0.05 sec)

mysql> select * from t_dept;
+--------+-------+-----+
| deptno | dname | loc |
+--------+-------+-----+
|      1 | RD    | SZ  |
|      2 | TEST  | BJ  |
+--------+-------+-----+
2 rows in set (0.00 sec)
```

另外的写法一
```
mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT AUTO_INCREMENT,
    -> dname VARCHAR(20)  NOT NULL,# 唯一
    -> loc   VARCHAR(40) NOT NULL,  #非空
    -> CONSTRAINT pk_deptno PRIMARY KEY   (deptno),
    -> CONSTRAINT uk_dname UNIQUE  (dname)
    ->
    -> );
Query OK, 0 rows affected (0.29 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+----------------+
| Field  | Type        | Null | Key | Default | Extra          |
+--------+-------------+------+-----+---------+----------------+
| deptno | int(11)     | NO   | PRI | NULL    | auto_increment |
| dname  | varchar(20) | NO   | UNI | NULL    |                |
| loc    | varchar(40) | NO   |     | NULL    |                |
+--------+-------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)
```
另外的写法二
```
mysql> CREATE TABLE IF NOT EXISTS t_dept(
    -> deptno INT AUTO_INCREMENT,
    -> dname VARCHAR(20)  NOT NULL,# 唯一
    -> loc   VARCHAR(40) NOT NULL,  #非空
    -> PRIMARY KEY   (deptno),
    ->  UNIQUE  (dname)
    ->
    -> );
Query OK, 0 rows affected (0.22 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+----------------+
| Field  | Type        | Null | Key | Default | Extra          |
+--------+-------------+------+-----+---------+----------------+
| deptno | int(11)     | NO   | PRI | NULL    | auto_increment |
| dname  | varchar(20) | NO   | UNI | NULL    |                |
| loc    | varchar(40) | NO   |     | NULL    |                |
+--------+-------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)

```

## 外键约束（FOREIGN KEY FK）

外键约束主要用于约束2个表之间的父子关系，即子表中某个字段的取值范围由父表决定。
如表示一个部门和员工关系，每个部门有多个员工，首先有2个表，部门表和员工表，然后员工表中有一个表示部门编号的字段deptno,其依赖为部门表的主键，

CONSTRAINT 外表约束名称 FOREIGN KEY(属性) REFERENCES 表2(表2属性);

```
mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | NO   | PRI | NULL    |       |
| dname  | varchar(20) | NO   |     | NULL    |       |
| loc    | varchar(40) | NO   |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> CREATE TABLE IF NOT EXISTS t_employee (
    -> empno INT PRIMARY KEY,
    -> ename VARCHAR(20),
    -> job VARCHAR(40),
    -> MGR INT,
    -> Hiredate DATE,
    -> sal DOUBLE(10,2),
    -> comm DOUBLE(10,2),
    -> deptno INT,
    -> CONSTRAINT fk_deptno FOREIGN KEY(deptno) REFERENCES t_dept(deptno)
    -> );
Query OK, 0 rows affected (0.21 sec)

mysql> desc t_employee;
+----------+--------------+------+-----+---------+-------+
| Field    | Type         | Null | Key | Default | Extra |
+----------+--------------+------+-----+---------+-------+
| empno    | int(11)      | NO   | PRI | NULL    |       |
| ename    | varchar(20)  | YES  |     | NULL    |       |
| job      | varchar(40)  | YES  |     | NULL    |       |
| MGR      | int(11)      | YES  |     | NULL    |       |
| Hiredate | date         | YES  |     | NULL    |       |
| sal      | double(10,2) | YES  |     | NULL    |       |
| comm     | double(10,2) | YES  |     | NULL    |       |
| deptno   | int(11)      | YES  | MUL | NULL    |       |
+----------+--------------+------+-----+---------+-------+
8 rows in set (0.00 sec)
```

在员工表中插入数据时,deptno的值一定要在t_dept表中存在不然插入失败