---
title: mysql学习七视图的基本应用
date: 2017-02-13 14:36:46
categories: mysql
tags: mysql
---

## mysql学习七视图的基本应用

* 视图的列可以来自于不同的表，是表的抽象和在逻辑意义上建立的新关系
* 视图是由基本表（实表）产生的表（虚表）
* 视图的建立和删除不影响基本表
* 对视图内容的更新（添加、修改、删除）直接影响基本表
* 当视图来自多个表时，不允许添加和删除数据



### 创建视图和查看

```
create view view_name
    AS 查询语句

-----------
CREATE OR REPLACE VIEW  视图名
AS
语句
```

####查看
```
SHOW CREATE VIEW viewname
DESCRIBE | DESC viewname
 show table status from view;
```

####准备数据
```
DROP TABLE IF EXISTS `t_product`;

CREATE TABLE `t_product` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `price` decimal(5,2) unsigned DEFAULT '0.00',
  PRIMARY KEY (`id`)
) 

/*Data for the table `t_product` */

insert  into `t_product`(`id`,`name`,`price`) values (1,'apple','6.50'),(2,'banana','4.50'),(3,'orange','1.50'),(4,'pear','2.50');

```

####建立视图

```
CREATE VIEW  view_product
AS
SELECT id,NAME,price
FROM t_product;
```

####使用视图

```
mysql> select * from view_product;
+----+--------+-------+
| id | name   | price |
+----+--------+-------+
|  1 | apple  |  6.50 |
|  2 | banana |  4.50 |
|  3 | orange |  1.50 |
|  4 | pear   |  2.50 |
+----+--------+-------+
4 rows in set (0.00 sec)
```

#### 向视图插入数据

```
INSERT INTO view_product (NAME,price) VALUES('花生',3.33);

mysql> select * from view_product;
+----+--------+-------+
| id | name   | price |
+----+--------+-------+
|  1 | apple  |  6.50 |
|  2 | banana |  4.50 |
|  3 | orange |  1.50 |
|  4 | pear   |  2.50 |
|  5 | 花生   |  3.33 |
+----+--------+-------+
5 rows in set (0.05 sec)
```

#### 修改
```
mysql> UPDATE view_product SET price=5.55 WHERE NAME='花生';
Query OK, 1 row affected (0.04 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from view_product;
+----+--------+-------+
| id | name   | price |
+----+--------+-------+
|  1 | apple  |  6.50 |
|  2 | banana |  4.50 |
|  3 | orange |  1.50 |
|  4 | pear   |  2.50 |
|  5 | 花生   |  5.55 |
+----+--------+-------+
5 rows in set (0.00 sec)
```

#### 删除

```
mysql> delete from view_product where name='花生';
Query OK, 1 row affected (0.04 sec)

mysql> select * from view_product;
+----+--------+-------+
| id | name   | price |
+----+--------+-------+
|  1 | apple  |  6.50 |
|  2 | banana |  4.50 |
|  3 | orange |  1.50 |
|  4 | pear   |  2.50 |
+----+--------+-------+
4 rows in set (0.00 sec)
```

#### 修改视图
CREATE OR REPLACE VIEW  视图名
AS
语句

```
mysql> create or replace view_product
    -> as
    -> select name from t_product;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'view_product
as
select name from t_product' at line 1
mysql> create or replace view view_product
    -> as
    -> select name from t_product;
Query OK, 0 rows affected (0.02 sec)

mysql> select * from view_product;
+--------+
| name   |
+--------+
| apple  |
| banana |
| orange |
| pear   |
+--------+
4 rows in set (0.00 sec)
```

#### Alter 修改视图

ALTER VIWE 视图名
AS 
语句

```
mysql> alter view view_product
    -> as
    -> select name,price from t_product;
Query OK, 0 rows affected (0.05 sec)

mysql> select * from view_product;
+--------+-------+
| name   | price |
+--------+-------+
| apple  |  6.50 |
| banana |  4.50 |
| orange |  1.50 |
| pear   |  2.50 |
+--------+-------+
4 rows in set (0.00 sec)
```


#### 通过系统表查看视图
数据库information_schema，下有一个包含视图信息的表views;

```
mysql> desc views;
+----------------------+--------------+------+-----+---------+-------+
| Field                | Type         | Null | Key | Default | Extra |
+----------------------+--------------+------+-----+---------+-------+
| TABLE_CATALOG        | varchar(512) | NO   |     |         |       |
| TABLE_SCHEMA         | varchar(64)  | NO   |     |         |       |
| TABLE_NAME           | varchar(64)  | NO   |     |         |       |
| VIEW_DEFINITION      | longtext     | NO   |     | NULL    |       |
| CHECK_OPTION         | varchar(8)   | NO   |     |         |       |
| IS_UPDATABLE         | varchar(3)   | NO   |     |         |       |
| DEFINER              | varchar(93)  | NO   |     |         |       |
| SECURITY_TYPE        | varchar(7)   | NO   |     |         |       |
| CHARACTER_SET_CLIENT | varchar(32)  | NO   |     |         |       |
| COLLATION_CONNECTION | varchar(32)  | NO   |     |         |       |
+----------------------+--------------+------+-----+---------+-------+
10 rows in set (0.00 sec)

mysql> select * from views where table_name='view_product';
+---------------+--------------+--------------+------------------------------------------------------------------------------------------------------------------------------------------+--------------+--------------+----------------+---------------+----------------------+----------------------+
| TABLE_CATALOG | TABLE_SCHEMA | TABLE_NAME   | VIEW_DEFINITION                                                                                                                          | CHECK_OPTION | IS_UPDATABLE | DEFINER        | SECURITY_TYPE | CHARACTER_SET_CLIENT | COLLATION_CONNECTION |
+---------------+--------------+--------------+------------------------------------------------------------------------------------------------------------------------------------------+--------------+--------------+----------------+---------------+----------------------+----------------------+
| def           | view         | view_product | select `view`.`t_product`.`id` AS `id`,`view`.`t_product`.`name` AS `name`,`view`.`t_product`.`price` AS `price` from `view`.`t_product` | NONE         | YES          | root@localhost | DEFINER       | utf8                 | utf8_general_ci      |
+---------------+--------------+--------------+------------------------------------------------------------------------------------------------------------------------------------------+--------------+--------------+----------------+---------------+----------------------+----------------------+
1 row in set (0.00 sec)

mysql> select * from views where table_name='view_product'\G
*************************** 1. row ***************************
       TABLE_CATALOG: def
        TABLE_SCHEMA: view
          TABLE_NAME: view_product
     VIEW_DEFINITION: select `view`.`t_product`.`id` AS `id`,`view`.`t_product`.`name` AS `name`,`view`.`t_product`.`price` AS `price` from `view`.`t_product`
        CHECK_OPTION: NONE
        IS_UPDATABLE: YES
             DEFINER: root@localhost
       SECURITY_TYPE: DEFINER
CHARACTER_SET_CLIENT: utf8
COLLATION_CONNECTION: utf8_general_ci
1 row in set (0.00 sec)
```

#### 删除视图

DROP VIEW 视图名1，视图名2 。。。

```
mysql> drop view if exists view_product;
Query OK, 0 rows affected (0.00 sec)

mysql> show tables;
+----------------+
| Tables_in_view |
+----------------+
| t_product      |
+----------------+
1 row in set (0.00 sec)

```
删除成功