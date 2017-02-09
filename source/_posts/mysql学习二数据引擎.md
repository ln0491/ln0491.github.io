---
title: mysql学习二数据引擎
date: 2017-02-09 13:54:50
categories: mysql
tags: mysql
---

## mysql学习二数据引擎

### 查看当前数据库支持的引擎

```
show engines

+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.00 sec)
```
或者
```
show engines \G

mysql> show engines \G
*************************** 1. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 2. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 4. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 5. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: CSV
     Support: YES
     Comment: CSV storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: Archive storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 8. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 9. row ***************************
      Engine: FEDERATED
     Support: NO
     Comment: Federated MySQL storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
9 rows in set (0.00 sec)
```

* Engine   引擎的名称
* Support   是否支付YES表示支持，NO表示不支持
* Comment  评价或者备注 Defalut表示，默认支持的引擎
* Transactions  是否支持事务，YES表示支持，NO表示不支持
* XA          所有支持的分布式是否符合XA规范，YES表示支持，NO表示不支持
* Savepoints  是否支持事务处理中的保存点，YES表示支持，NO表示不支持

或者
show variables like 'have%'
```
mysql> show variables like 'have%';
+------------------------+----------+
| Variable_name          | Value    |
+------------------------+----------+
| have_compress          | YES      |
| have_crypt             | NO       |
| have_dynamic_loading   | YES      |
| have_geometry          | YES      |
| have_openssl           | DISABLED |
| have_profiling         | YES      |
| have_query_cache       | YES      |
| have_rtree_keys        | YES      |
| have_ssl               | DISABLED |
| have_statement_timeout | YES      |
| have_symlink           | YES      |
+------------------------+----------+
11 rows in set, 1 warning (0.00 sec)
```

* Variable_name  引擎名称
* value 是否支持YES支持，NO不支持,DISABLED表示支持但未启用

### 查看默认引擎
show variables like '%storage_engine%'
```
mysql> show variables like '%storage_engine%';
+----------------------------------+--------+
| Variable_name                    | Value  |
+----------------------------------+--------+
| default_storage_engine           | InnoDB |
| default_tmp_storage_engine       | InnoDB |
| disabled_storage_engines         |        |
| internal_tmp_disk_storage_engine | InnoDB |
+----------------------------------+--------+
4 rows in set, 1 warning (0.00 sec)
```
InnoDB 为默认引擎

### 修改默认引擎

my.ini文件

```
[mysqld]

# The next three options are mutually exclusive to SERVER_PORT below.
# skip-networking

# enable-named-pipe

# shared-memory

# shared-memory-base-name=MYSQL

# The Pipe the MySQL Server will use
# socket=MYSQL

# The TCP/IP Port the MySQL Server will listen on 默认端口号
port=3306

# Path to installation directory. All paths are usually resolved relative to this.  服务器的默认安装目录
# basedir="C:/Program Files/MySQL/MySQL Server 5.7/"

# Path to the database root   数据库数据文件的目录
datadir=C:/ProgramData/MySQL/MySQL Server 5.7\Data

# The default character set that will be used when a new schema or table is
# created and no character set is defined  修改服务器默认字符
character-set-server=utf8

# The default storage engine that will be used when create new tables when
# 这里修改默认引擎
default-storage-engine=INNODB
```

修改后重启Mysql服务