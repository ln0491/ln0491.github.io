---
title: mysql学习八触发器基本操作
date: 2017-02-13 16:02:11
categories: mysql
tags: mysql
---

###  mysql学习八触发器基本操作


#### 创建

```
CREATE OR REPLACE TRIGGER 触发器名称

BEFORE | AFTER 触发器事件trigger_event

ON 表名TABLE_NAME FOR EACH ROW trigger_stmt
```
* 触发器名称 						trigger_xxx
* BEFORE | AFTER 			参数指定触发器执行的时间
* 触发器事件trigger_event 		包含DELETE,INSERT,UPDATE
* 表名TABLE_NAME 				触发事件的操作的表名
* FOR EACH ROW 					参数表示任何一条记录上的操作满足触发条件都会触发这个触发器
* trigger_stmt  激活触发器后被执行的语句

```
mysql> use company;
Database changed
mysql> show tables;
Empty set (0.00 sec)

mysql> CREATE TABLE `t_dept` (
    ->   `deptno` INT(11) ,
    ->   `dname` VARCHAR(20) ,
    ->   `loc` VARCHAR(40)
    ->
    ->
    -> );
Query OK, 0 rows affected (0.20 sec)

mysql> CREATE TABLE `t_diary` (
    ->   diaryno INT PRIMARY KEY AUTO_INCREMENT,
    ->   tablename VARCHAR(20),
    ->   diarytime DATETIME
    ->
    ->
    -> );
Query OK, 0 rows affected (0.19 sec)

mysql> show tables;
+-------------------+
| Tables_in_company |
+-------------------+
| t_dept            |
| t_diary           |
+-------------------+
2 rows in set (0.00 sec)

mysql> desc t_dept;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| deptno | int(11)     | YES  |     | NULL    |       |
| dname  | varchar(20) | YES  |     | NULL    |       |
| loc    | varchar(40) | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> desc t_diary;
+-----------+------------+------+-----+---------+----------------+
| Field     | Type       | Null | Key | Default | Extra          |
+-----------+------------+------+-----+---------+----------------+
| diaryno   | int(11)    | NO   | PRI | NULL    | auto_increment |
| tablename | varchar(2) | YES  |     | NULL    |                |
| diarytime | datetime   | YES  |     | NULL    |                |
+-----------+------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)
```
创建
```
mysql> CREATE  TRIGGER tri_diarytime
    -> BEFORE INSERT
    -> ON t_dept FOR EACH ROW
    -> INSERT INTO t_diary VALUES(NULL,'t_dept',NOW());
Query OK, 0 rows affected (0.07 sec)
```
插入一条记录到t_dept

```
mysql> INSERT INTO t_dept VALUE(1,'sz','szn');
Query OK, 1 row affected (0.08 sec)

mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|      1 | sz    | szn  |
+--------+-------+------+
1 row in set (0.00 sec)

mysql> select * from t_diary;
+---------+-----------+---------------------+
| diaryno | tablename | diarytime           |
+---------+-----------+---------------------+
|       1 | t_dept    | 2017-02-13 16:29:41 |
+---------+-----------+---------------------+
1 row in set (0.00 sec)

mysql> INSERT INTO t_dept VALUE(2,'sz','szn'),(3,'test','sh');
Query OK, 2 rows affected (0.10 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from t_dept;
+--------+-------+------+
| deptno | dname | loc  |
+--------+-------+------+
|      1 | sz    | szn  |
|      2 | sz    | szn  |
|      3 | test  | sh   |
+--------+-------+------+
3 rows in set (0.00 sec)

mysql> select * from t_diary;
+---------+-----------+---------------------+
| diaryno | tablename | diarytime           |
+---------+-----------+---------------------+
|       1 | t_dept    | 2017-02-13 16:29:41 |
|       2 | t_dept    | 2017-02-13 16:30:24 |
|       3 | t_dept    | 2017-02-13 16:30:24 |
+---------+-----------+---------------------+
3 rows in set (0.00 sec)
```

第二种创建
```

DELIMITER $$

CREATE  TRIGGER tri_diarytime
BEFORE INSERT
ON t_dept FOR EACH ROW
BEGIN
INSERT INTO t_diary VALUES(NULL,'t_dept',NOW());
INSERT INTO t_diary VALUES(NULL,'t_dept',NOW());
END
$$;
DELIMITER;
```

#### 查看
show triggers \G
show triggers;
```
mysql> show triggers \G
*************************** 1. row ***************************
             Trigger: tri_diarytime
               Event: INSERT
               Table: t_dept
           Statement: begin
INSERT INTO t_diary VALUES(NULL,'t_dept',NOW());
INSERT INTO t_diary VALUES(NULL,'t_dept',NOW());
end
              Timing: BEFORE
             Created: 2017-02-13 16:35:31.52
            sql_mode: STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
             Definer: root@localhost
character_set_client: utf8
collation_connection: utf8_general_ci
  Database Collation: utf8_general_ci
1 row in set (0.00 sec)
```
#### 通过information_schma查看triggers
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| company            |
| databasetest       |
| mysql              |
| performance_schema |
| sakila             |
| sys                |
| view               |
| world              |
+--------------------+
9 rows in set (0.00 sec)

mysql> use information_schema;
Database changed
mysql> desc triggers;
+----------------------------+---------------+------+-----+---------+-------+
| Field                      | Type          | Null | Key | Default | Extra |
+----------------------------+---------------+------+-----+---------+-------+
| TRIGGER_CATALOG            | varchar(512)  | NO   |     |         |       |
| TRIGGER_SCHEMA             | varchar(64)   | NO   |     |         |       |
| TRIGGER_NAME               | varchar(64)   | NO   |     |         |       |
| EVENT_MANIPULATION         | varchar(6)    | NO   |     |         |       |
| EVENT_OBJECT_CATALOG       | varchar(512)  | NO   |     |         |       |
| EVENT_OBJECT_SCHEMA        | varchar(64)   | NO   |     |         |       |
| EVENT_OBJECT_TABLE         | varchar(64)   | NO   |     |         |       |
| ACTION_ORDER               | bigint(4)     | NO   |     | 0       |       |
| ACTION_CONDITION           | longtext      | YES  |     | NULL    |       |
| ACTION_STATEMENT           | longtext      | NO   |     | NULL    |       |
| ACTION_ORIENTATION         | varchar(9)    | NO   |     |         |       |
| ACTION_TIMING              | varchar(6)    | NO   |     |         |       |
| ACTION_REFERENCE_OLD_TABLE | varchar(64)   | YES  |     | NULL    |       |
| ACTION_REFERENCE_NEW_TABLE | varchar(64)   | YES  |     | NULL    |       |
| ACTION_REFERENCE_OLD_ROW   | varchar(3)    | NO   |     |         |       |
| ACTION_REFERENCE_NEW_ROW   | varchar(3)    | NO   |     |         |       |
| CREATED                    | datetime(2)   | YES  |     | NULL    |       |
| SQL_MODE                   | varchar(8192) | NO   |     |         |       |
| DEFINER                    | varchar(93)   | NO   |     |         |       |
| CHARACTER_SET_CLIENT       | varchar(32)   | NO   |     |         |       |
| COLLATION_CONNECTION       | varchar(32)   | NO   |     |         |       |
| DATABASE_COLLATION         | varchar(32)   | NO   |     |         |       |
+----------------------------+---------------+------+-----+---------+-------+
22 rows in set (0.00 sec)


mysql> select * from triggers where trigger_name='tri_diarytime' \G
*************************** 1. row ***************************
           TRIGGER_CATALOG: def
            TRIGGER_SCHEMA: company
              TRIGGER_NAME: tri_diarytime
        EVENT_MANIPULATION: INSERT
      EVENT_OBJECT_CATALOG: def
       EVENT_OBJECT_SCHEMA: company
        EVENT_OBJECT_TABLE: t_dept
              ACTION_ORDER: 1
          ACTION_CONDITION: NULL
          ACTION_STATEMENT: begin
INSERT INTO t_diary VALUES(NULL,'t_dept',NOW());
INSERT INTO t_diary VALUES(NULL,'t_dept',NOW());
end
        ACTION_ORIENTATION: ROW
             ACTION_TIMING: BEFORE
ACTION_REFERENCE_OLD_TABLE: NULL
ACTION_REFERENCE_NEW_TABLE: NULL
  ACTION_REFERENCE_OLD_ROW: OLD
  ACTION_REFERENCE_NEW_ROW: NEW
                   CREATED: 2017-02-13 16:35:31.52
                  SQL_MODE: STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
                   DEFINER: root@localhost
      CHARACTER_SET_CLIENT: utf8
      COLLATION_CONNECTION: utf8_general_ci
        DATABASE_COLLATION: utf8_general_ci
1 row in set (0.01 sec)
```
#### 删除

DROP TRIGGER IF EXISTS 触发器名称

```
drop trigger if exists tri_diarytime;
mysql> show triggers;
Empty set (0.00 sec)
```

删除成功