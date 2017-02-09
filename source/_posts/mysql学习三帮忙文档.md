---
title: mysql学习三帮忙文档
date: 2017-02-09 14:24:45
categories: mysql
tags: mysql
---

## mysql学习三帮忙文档

### 查看系统帮助
help contents
```
mysql> help contents;
You asked for help about help category: "Contents"
For more information, type 'help <item>', where <item> is one of the following
categories:
   Account Management
   Administration
   Compound Statements
   Data Definition
   Data Manipulation
   Data Types
   Functions
   Functions and Modifiers for Use with GROUP BY
   Geographic Features
   Help Metadata
   Language Structure
   Plugins
   Procedures
   Storage Engines
   Table Maintenance
   Transactions
   User-Defined Functions
   Utility
```

帮助文档的目录列表

### 查看数据类型
help data types
```
mysql> help data types;
You asked for help about help category: "Data Types"
For more information, type 'help <item>', where <item> is one of the following
topics:
   AUTO_INCREMENT
   BIGINT
   BINARY
   BIT
   BLOB
   BLOB DATA TYPE
   BOOLEAN
   CHAR
   CHAR BYTE
   DATE
   DATETIME
   DEC
   DECIMAL
   DOUBLE
   DOUBLE PRECISION
   ENUM
   FLOAT
   INT
   INTEGER
   LONGBLOB
   LONGTEXT
   MEDIUMBLOB
   MEDIUMINT
   MEDIUMTEXT
   SET DATA TYPE
   SMALLINT
   TEXT
   TIME
   TIMESTAMP
   TINYBLOB
   TINYINT
   TINYTEXT
   VARBINARY
   VARCHAR
   YEAR DATA TYPE
```

### 查看整数

```
mysql> help int;
Name: 'INT'
Description:
INT[(M)] [UNSIGNED] [ZEROFILL]

A normal-size integer. The signed range is -2147483648 to 2147483647.
The unsigned range is 0 to 4294967295.

URL: http://dev.mysql.com/doc/refman/5.7/en/numeric-type-overview.html


mysql> help tinyint;
Name: 'TINYINT'
Description:
TINYINT[(M)] [UNSIGNED] [ZEROFILL]

A very small integer. The signed range is -128 to 127. The unsigned
range is 0 to 255.

URL: http://dev.mysql.com/doc/refman/5.7/en/numeric-type-overview.html
```