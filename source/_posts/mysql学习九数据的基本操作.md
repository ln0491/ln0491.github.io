---
title: mysql学习九数据的基本操作
date: 2017-02-14 10:15:21
categories: mysql
tags: mysql
---

### mysql学习九数据的基本操作

实际上就是对表的数据进行操作
CRUD

* C--CREATE 插入 
* R--READ 查询
* U--UPDATE 更新
* D--DELETE 删除

#### 插入完整数据记录

```
INSERT INTO table_name(field1,field2,field3,……fieldn)
    VALUES(value11,value21,value31……valuen1),
            (value12,value22,value32……valuen2),
            (value13,value23,value33……valuen3),
            ……
            (value1m,value2m,value3m……valuenm)

```

创建表
```
CREATE TABLE `t_dept` (
  `deptno` int(11) DEFAULT NULL,
  `dname` varchar(20) DEFAULT NULL,
  `loc` varchar(40) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
插入记录
```
mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | NO   |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> insert into t_dept(deptno,dname,loc) values(1,'开发','深圳');
Query OK, 1 row affected (0.04 sec)

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 深圳   |
+--------+--------+--------+
1 row in set (0.00 sec)
```

#### 插入了一部分数据记录
```
INSERT INTO 表名(field1,field2,...) values(value1,valu2,...);
```

```
mysql> insert into t_dept(dname,loc) values('测试','上海');
Query OK, 1 row affected (0.04 sec)

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 深圳   |
|   NULL | 测试   | 上海   |
+--------+--------+--------+
2 rows in set (0.00 sec)
```



#### 插入了多条数据记录

```
INSERT INTO 表名(field1,field2,...) values(value1,valu2,...),(value1,valu2,...),....;
```

插入记录
```
mysql> INSERT INTO t_dept VALUES(2,'sz','szn'),(3,'test','sh'),(4,'销售','北京'),(5,'人事','重庆');
Query OK, 4 rows affected (0.04 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 深圳   |
|   NULL | 测试   | 上海   |
|      2 | sz     | szn    |
|      3 | test   | sh     |
|      4 | 销售   | 北京   |
|      5 | 人事   | 重庆   |
+--------+--------+--------+
6 rows in set (0.00 sec)
```

#### 插入了查询结果
```
INSERT INTO table_name1(field11,field12,field13,……field1n)
    SELECT (field21,field22,field23,……field2n)
        FROM table_name2
            WHERE ……


```
插入查询结果
```
mysql> INSERT INTO t_dept select * from t_dept where deptno is not null;
Query OK, 5 rows affected (0.05 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 深圳   |
|   NULL | 测试   | 上海   |
|      2 | sz     | szn    |
|      3 | test   | sh     |
|      4 | 销售   | 北京   |
|      5 | 人事   | 重庆   |
|      1 | 开发   | 深圳   |
|      2 | sz     | szn    |
|      3 | test   | sh     |
|      4 | 销售   | 北京   |
|      5 | 人事   | 重庆   |
+--------+--------+--------+
11 rows in set (0.00 sec)
```

#### 更新记录

```
UPDATE table_name
    SET field1=value1,
        field2=value2,
        field3=value3,
    WHERE CONDITION

```
更新所有deptno不是NULL的loc为广州
```
mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 深圳   |
|   NULL | 测试   | 上海   |
|      2 | sz     | szn    |
|      3 | test   | sh     |
|      4 | 销售   | 北京   |
|      5 | 人事   | 重庆   |
|      1 | 开发   | 深圳   |
|      2 | sz     | szn    |
|      3 | test   | sh     |
|      4 | 销售   | 北京   |
|      5 | 人事   | 重庆   |
+--------+--------+--------+
11 rows in set (0.00 sec)


mysql> update t_dept set loc='广州' where deptno is not null;
Query OK, 10 rows affected (0.05 sec)
Rows matched: 10  Changed: 10  Warnings: 0

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 广州   |
|   NULL | 测试   | 上海   |
|      2 | sz     | 广州   |
|      3 | test   | 广州   |
|      4 | 销售   | 广州   |
|      5 | 人事   | 广州   |
|      1 | 开发   | 广州   |
|      2 | sz     | 广州   |
|      3 | test   | 广州   |
|      4 | 销售   | 广州   |
|      5 | 人事   | 广州   |
+--------+--------+--------+
11 rows in set (0.00 sec)
```

#### 删除记录

```
DELETE [LOW_PRIORITY] [QUICK] [IGNORE] FROM tbl_name
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]

```
删除deptno 为null的记录


```
mysql> delete from t_dept where deptno is null;
Query OK, 1 row affected (0.05 sec)

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 广州   |
|      2 | sz     | 广州   |
|      3 | test   | 广州   |
|      4 | 销售   | 广州   |
|      5 | 人事   | 广州   |
|      1 | 开发   | 广州   |
|      2 | sz     | 广州   |
|      3 | test   | 广州   |
|      4 | 销售   | 广州   |
|      5 | 人事   | 广州   |
+--------+--------+--------+
10 rows in set (0.00 sec)
```
#### 删除所有记录

```
DELETE FROM 表名
```
```
mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | 开发   | 广州   |
|      2 | sz     | 广州   |
|      3 | test   | 广州   |
|      4 | 销售   | 广州   |
|      5 | 人事   | 广州   |
|      1 | 开发   | 广州   |
|      2 | sz     | 广州   |
|      3 | test   | 广州   |
|      4 | 销售   | 广州   |
|      5 | 人事   | 广州   |
+--------+--------+--------+
10 rows in set (0.00 sec)

mysql> delete from t_dept;
Query OK, 10 rows affected (0.05 sec)

mysql> select * from t_dept;
Empty set (0.00 sec)
```

#### 清空表
```
truncate  表名
```
数据表
```
mysql> CREATE TABLE t_dept (
    ->   deptno INT(11)  PRIMARY KEY AUTO_INCREMENT,
    ->   dname VARCHAR(20) ,
    ->   loc VARCHAR(40)
    -> );
Query OK, 0 rows affected (0.19 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+----------------+
| Field  | Type        | Null | Key | Default | Extra          |
+--------+-------------+------+-----+---------+----------------+
| deptno | int(11)     | NO   | PRI | NULL    | auto_increment |
| dname  | varchar(20) | YES  |     | NULL    |                |
| loc    | varchar(40) | YES  |     | NULL    |                |
+--------+-------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)

mysql> INSERT INTO t_dept VALUES('sz','szn'),('test','sh'),('销售','北京'),('人事','重庆');
ERROR 1136 (21S01): Column count doesn't match value count at row 1
mysql> INSERT INTO t_dept (dname,loc) VALUES('sz','szn'),('test','sh'),('销售','北京'),('人事','重庆');
Query OK, 4 rows affected (0.05 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | sz     | szn    |
|      2 | test   | sh     |
|      3 | 销售   | 北京   |
|      4 | 人事   | 重庆   |
+--------+--------+--------+
4 rows in set (0.00 sec)
```
清空
```
mysql> TRUNCATE t_dept;
Query OK, 0 rows affected (0.22 sec)

mysql> select * from t_dept;
Empty set (0.00 sec)

mysql> INSERT INTO t_dept (dname,loc) VALUES('sz','szn'),('test','sh'),('销售','北京'),('人事','重庆');
Query OK, 4 rows affected (0.05 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> select * from t_dept;
+--------+--------+--------+
| deptno | dname  | loc    |
+--------+--------+--------+
|      1 | sz     | szn    |
|      2 | test   | sh     |
|      3 | 销售   | 北京   |
|      4 | 人事   | 重庆   |
+--------+--------+--------+
4 rows in set (0.00 sec)
```
TRUNCATE 会把表中的数据和主键同时都复位DELETE 不会
但是使用delete清空表中的记录，内容的ID仍然从删除点的ID继续建立，而不是从1开始，而truncate相当于保留了表的结构而重新建立了一张同样的新表。

效率上truncate比delete快，但truncate删除后不记录mysql日志，不可以恢复数据。

delete的效果有点像将mysql表中所有记录一条一条删除到删完。

#### 查询记录语法

```
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
      [HIGH_PRIORITY]
      [STRAIGHT_JOIN]
      [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
      [SQL_CACHE | SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr, ...
    [FROM table_references
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}
      [ASC | DESC], ... [WITH ROLLUP]]
    [HAVING where_condition]
    [ORDER BY {col_name | expr | position}
      [ASC | DESC], ...]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    [PROCEDURE procedure_name(argument_list)]
    [INTO OUTFILE 'file_name' export_options
      | INTO DUMPFILE 'file_name']
    [FOR UPDATE | LOCK IN SHARE MODE]]

```
