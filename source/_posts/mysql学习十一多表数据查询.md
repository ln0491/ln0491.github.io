---
title: mysql学习十一多表数据查询
date: 2017-02-16 10:10:47
categories: mysql
tags: mysql
---

### mysql学习十一多表数据查询
多表查询分为：内连接，外连接，子查询

#### UNION与UNION ALL
UNION用的比较多union all是直接连接，取到得是所有值，记录可能有重复   union 是取唯一值，记录没有重复 
  1、UNION 的语法如下：
     [SQL 语句 1]
      UNION
     [SQL 语句 2]
2、UNION ALL 的语法如下：
     [SQL 语句 1]
      UNION ALL
     [SQL 语句 2]
效率：
UNION和UNION ALL关键字都是将两个结果集合并为一个，但这两者从使用和效率上来说都有所不同。
1、对重复结果的处理：UNION在进行表链接后会筛选掉重复的记录，Union All不会去除重复记录。
2、对排序的处理：Union将会按照字段的顺序进行排序；UNION ALL只是简单的将两个结果合并后就返回。
从效率上说，UNION ALL 要比UNION快很多，所以，如果可以确认合并的两个结果集中不包含重复数据且不需要排序时的话，那么就使用UNION ALL。

#### 笛卡尔积

查询所有员工的信息和部门信息

```
mysql> select * from emp ,dept;
+-------+--------+-----------+------+---------------------+---------+---------+--------+--------+------------+----------+
| empno | ename  | job       | mgr  | hiredate            | sal     | comm    | deptno | deptno | dname      | loc      |
+-------+--------+-----------+------+---------------------+---------+---------+--------+--------+------------+----------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 00:00:00 |  800.00 |    NULL |     20 |     10 | ACCOUNTING | NEW YORK |
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 00:00:00 |  800.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 00:00:00 |  800.00 |    NULL |     20 |     30 | SALES      | CHICAGO  |
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 00:00:00 |  800.00 |    NULL |     20 |     40 | OPERATIONS | BOSTON   |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 00:00:00 | 1600.00 |  300.00 |     30 |     10 | ACCOUNTING | NEW YORK |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 00:00:00 | 1600.00 |  300.00 |     30 |     20 | RESEARCH   | DALLAS   |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 00:00:00 | 1600.00 |  300.00 |     30 |     30 | SALES      | CHICAGO  |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 00:00:00 | 1600.00 |  300.00 |     30 |     40 | OPERATIONS | BOSTON   |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 00:00:00 | 1250.00 |  500.00 |     30 |     10 | ACCOUNTING | NEW YORK |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 00:00:00 | 1250.00 |  500.00 |     30 |     20 | RESEARCH   | DALLAS   |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 00:00:00 | 1250.00 |  500.00 |     30 |     30 | SALES      | CHICAGO  |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 00:00:00 | 1250.00 |  500.00 |     30 |     40 | OPERATIONS | BOSTON   |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 |    NULL |     20 |     10 | ACCOUNTING | NEW YORK |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 |    NULL |     20 |     30 | SALES      | CHICAGO  |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 |    NULL |     20 |     40 | OPERATIONS | BOSTON   |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 00:00:00 | 1250.00 | 1400.00 |     30 |     10 | ACCOUNTING | NEW YORK |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 00:00:00 | 1250.00 | 1400.00 |     30 |     20 | RESEARCH   | DALLAS   |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 00:00:00 | 1250.00 | 1400.00 |     30 |     30 | SALES      | CHICAGO  |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 00:00:00 | 1250.00 | 1400.00 |     30 |     40 | OPERATIONS | BOSTON   |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 |    NULL |     30 |     10 | ACCOUNTING | NEW YORK |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 |    NULL |     30 |     20 | RESEARCH   | DALLAS   |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 |    NULL |     30 |     30 | SALES      | CHICAGO  |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 |    NULL |     30 |     40 | OPERATIONS | BOSTON   |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 |    NULL |     10 |     10 | ACCOUNTING | NEW YORK |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 |    NULL |     10 |     20 | RESEARCH   | DALLAS   |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 |    NULL |     10 |     30 | SALES      | CHICAGO  |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 |    NULL |     10 |     40 | OPERATIONS | BOSTON   |
|  7788 | SCOTT  | ANALYST   | 7566 | 1982-12-09 00:00:00 | 3000.00 |    NULL |     20 |     10 | ACCOUNTING | NEW YORK |
|  7788 | SCOTT  | ANALYST   | 7566 | 1982-12-09 00:00:00 | 3000.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7788 | SCOTT  | ANALYST   | 7566 | 1982-12-09 00:00:00 | 3000.00 |    NULL |     20 |     30 | SALES      | CHICAGO  |
|  7788 | SCOTT  | ANALYST   | 7566 | 1982-12-09 00:00:00 | 3000.00 |    NULL |     20 |     40 | OPERATIONS | BOSTON   |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 |    NULL |     10 |     10 | ACCOUNTING | NEW YORK |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 |    NULL |     10 |     20 | RESEARCH   | DALLAS   |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 |    NULL |     10 |     30 | SALES      | CHICAGO  |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 |    NULL |     10 |     40 | OPERATIONS | BOSTON   |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 00:00:00 | 1500.00 |    0.00 |     30 |     10 | ACCOUNTING | NEW YORK |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 00:00:00 | 1500.00 |    0.00 |     30 |     20 | RESEARCH   | DALLAS   |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 00:00:00 | 1500.00 |    0.00 |     30 |     30 | SALES      | CHICAGO  |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 00:00:00 | 1500.00 |    0.00 |     30 |     40 | OPERATIONS | BOSTON   |
|  7876 | ADAMS  | CLERK     | 7788 | 1983-01-12 00:00:00 | 1100.00 |    NULL |     20 |     10 | ACCOUNTING | NEW YORK |
|  7876 | ADAMS  | CLERK     | 7788 | 1983-01-12 00:00:00 | 1100.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7876 | ADAMS  | CLERK     | 7788 | 1983-01-12 00:00:00 | 1100.00 |    NULL |     20 |     30 | SALES      | CHICAGO  |
|  7876 | ADAMS  | CLERK     | 7788 | 1983-01-12 00:00:00 | 1100.00 |    NULL |     20 |     40 | OPERATIONS | BOSTON   |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 00:00:00 |  950.00 |    NULL |     30 |     10 | ACCOUNTING | NEW YORK |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 00:00:00 |  950.00 |    NULL |     30 |     20 | RESEARCH   | DALLAS   |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 00:00:00 |  950.00 |    NULL |     30 |     30 | SALES      | CHICAGO  |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 00:00:00 |  950.00 |    NULL |     30 |     40 | OPERATIONS | BOSTON   |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 |    NULL |     20 |     10 | ACCOUNTING | NEW YORK |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 |    NULL |     20 |     30 | SALES      | CHICAGO  |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 |    NULL |     20 |     40 | OPERATIONS | BOSTON   |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 00:00:00 | 1300.00 |    NULL |     10 |     10 | ACCOUNTING | NEW YORK |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 00:00:00 | 1300.00 |    NULL |     10 |     20 | RESEARCH   | DALLAS   |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 00:00:00 | 1300.00 |    NULL |     10 |     30 | SALES      | CHICAGO  |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 00:00:00 | 1300.00 |    NULL |     10 |     40 | OPERATIONS | BOSTON   |
+-------+--------+-----------+------+---------------------+---------+---------+--------+--------+------------+----------+
56 rows in set (0.01 sec)

```

原来只有14条记录的，2张表一起查就变56条，
消除笛卡尔积

```
mysql> select * from emp e,dept d where e.deptno=d.deptno;
+-------+--------+-----------+------+---------------------+---------+---------+--------+--------+------------+----------+
| empno | ename  | job       | mgr  | hiredate            | sal     | comm    | deptno | deptno | dname      | loc      |
+-------+--------+-----------+------+---------------------+---------+---------+--------+--------+------------+----------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 00:00:00 |  800.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 00:00:00 | 1600.00 |  300.00 |     30 |     30 | SALES      | CHICAGO  |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 00:00:00 | 1250.00 |  500.00 |     30 |     30 | SALES      | CHICAGO  |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 00:00:00 | 1250.00 | 1400.00 |     30 |     30 | SALES      | CHICAGO  |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 |    NULL |     30 |     30 | SALES      | CHICAGO  |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 |    NULL |     10 |     10 | ACCOUNTING | NEW YORK |
|  7788 | SCOTT  | ANALYST   | 7566 | 1982-12-09 00:00:00 | 3000.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 |    NULL |     10 |     10 | ACCOUNTING | NEW YORK |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 00:00:00 | 1500.00 |    0.00 |     30 |     30 | SALES      | CHICAGO  |
|  7876 | ADAMS  | CLERK     | 7788 | 1983-01-12 00:00:00 | 1100.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 00:00:00 |  950.00 |    NULL |     30 |     30 | SALES      | CHICAGO  |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 |    NULL |     20 |     20 | RESEARCH   | DALLAS   |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 00:00:00 | 1300.00 |    NULL |     10 |     10 | ACCOUNTING | NEW YORK |
+-------+--------+-----------+------+---------------------+---------+---------+--------+--------+------------+----------+
14 rows in set (0.00 sec)
```
#### 内连接 INNER JION
连接分为内连接 INNER JION，外连接 OUTER JION，交叉连接 CROSS JION

内连接： 在表关系的笛卡尔积数据记录中，保留表关系中所有匹配的数据记录，舍弃不匹配的数据记录
分为自然连接、等值连接，不等连接



#### 查询每个员工的编号、姓名、职位、基本工资、部门名称、部门位置

```
mysql> SELECT e.empno,e.ename,e.job,e.sal,d.dname,d.loc
    -> FROM emp e ,dept d
    -> WHERE e.deptno = d.deptno;
+-------+--------+-----------+---------+------------+----------+
| empno | ename  | job       | sal     | dname      | loc      |
+-------+--------+-----------+---------+------------+----------+
|  7369 | SMITH  | CLERK     |  800.00 | RESEARCH   | DALLAS   |
|  7499 | ALLEN  | SALESMAN  | 1600.00 | SALES      | CHICAGO  |
|  7521 | WARD   | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7566 | JONES  | MANAGER   | 2975.00 | RESEARCH   | DALLAS   |
|  7654 | MARTIN | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7698 | BLAKE  | MANAGER   | 2850.00 | SALES      | CHICAGO  |
|  7782 | CLARK  | MANAGER   | 2450.00 | ACCOUNTING | NEW YORK |
|  7788 | SCOTT  | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7839 | KING   | PRESIDENT | 5000.00 | ACCOUNTING | NEW YORK |
|  7844 | TURNER | SALESMAN  | 1500.00 | SALES      | CHICAGO  |
|  7876 | ADAMS  | CLERK     | 1100.00 | RESEARCH   | DALLAS   |
|  7900 | JAMES  | CLERK     |  950.00 | SALES      | CHICAGO  |
|  7902 | FORD   | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7934 | MILLER | CLERK     | 1300.00 | ACCOUNTING | NEW YORK |
+-------+--------+-----------+---------+------------+----------+
14 rows in set (0.00 sec)
```

第二种连接查询
```
mysql> SELECT e.empno,e.ename,e.job,e.sal,d.dname,d.loc
    -> FROM emp e INNER JOIN dept d ON e.deptno = d.deptno;
+-------+--------+-----------+---------+------------+----------+
| empno | ename  | job       | sal     | dname      | loc      |
+-------+--------+-----------+---------+------------+----------+
|  7369 | SMITH  | CLERK     |  800.00 | RESEARCH   | DALLAS   |
|  7499 | ALLEN  | SALESMAN  | 1600.00 | SALES      | CHICAGO  |
|  7521 | WARD   | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7566 | JONES  | MANAGER   | 2975.00 | RESEARCH   | DALLAS   |
|  7654 | MARTIN | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7698 | BLAKE  | MANAGER   | 2850.00 | SALES      | CHICAGO  |
|  7782 | CLARK  | MANAGER   | 2450.00 | ACCOUNTING | NEW YORK |
|  7788 | SCOTT  | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7839 | KING   | PRESIDENT | 5000.00 | ACCOUNTING | NEW YORK |
|  7844 | TURNER | SALESMAN  | 1500.00 | SALES      | CHICAGO  |
|  7876 | ADAMS  | CLERK     | 1100.00 | RESEARCH   | DALLAS   |
|  7900 | JAMES  | CLERK     |  950.00 | SALES      | CHICAGO  |
|  7902 | FORD   | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7934 | MILLER | CLERK     | 1300.00 | ACCOUNTING | NEW YORK |
+-------+--------+-----------+---------+------------+----------+
14 rows in set (0.00 sec)

```

结果相同
左连接
```
mysql> SELECT e.empno,e.ename,e.job,e.sal,d.dname,d.loc
    -> FROM emp e LEFT JOIN dept d ON e.deptno = d.deptno;
+-------+--------+-----------+---------+------------+----------+
| empno | ename  | job       | sal     | dname      | loc      |
+-------+--------+-----------+---------+------------+----------+
|  7782 | CLARK  | MANAGER   | 2450.00 | ACCOUNTING | NEW YORK |
|  7839 | KING   | PRESIDENT | 5000.00 | ACCOUNTING | NEW YORK |
|  7934 | MILLER | CLERK     | 1300.00 | ACCOUNTING | NEW YORK |
|  7369 | SMITH  | CLERK     |  800.00 | RESEARCH   | DALLAS   |
|  7566 | JONES  | MANAGER   | 2975.00 | RESEARCH   | DALLAS   |
|  7788 | SCOTT  | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7876 | ADAMS  | CLERK     | 1100.00 | RESEARCH   | DALLAS   |
|  7902 | FORD   | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7499 | ALLEN  | SALESMAN  | 1600.00 | SALES      | CHICAGO  |
|  7521 | WARD   | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7654 | MARTIN | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7698 | BLAKE  | MANAGER   | 2850.00 | SALES      | CHICAGO  |
|  7844 | TURNER | SALESMAN  | 1500.00 | SALES      | CHICAGO  |
|  7900 | JAMES  | CLERK     |  950.00 | SALES      | CHICAGO  |
+-------+--------+-----------+---------+------------+----------+
14 rows in set (0.00 sec)
```

右连接
```
mysql> SELECT e.empno,e.ename,e.job,e.sal,d.dname,d.loc
    -> FROM emp e RIGHT JOIN dept d ON e.deptno = d.deptno;
+-------+--------+-----------+---------+------------+----------+
| empno | ename  | job       | sal     | dname      | loc      |
+-------+--------+-----------+---------+------------+----------+
|  7369 | SMITH  | CLERK     |  800.00 | RESEARCH   | DALLAS   |
|  7499 | ALLEN  | SALESMAN  | 1600.00 | SALES      | CHICAGO  |
|  7521 | WARD   | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7566 | JONES  | MANAGER   | 2975.00 | RESEARCH   | DALLAS   |
|  7654 | MARTIN | SALESMAN  | 1250.00 | SALES      | CHICAGO  |
|  7698 | BLAKE  | MANAGER   | 2850.00 | SALES      | CHICAGO  |
|  7782 | CLARK  | MANAGER   | 2450.00 | ACCOUNTING | NEW YORK |
|  7788 | SCOTT  | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7839 | KING   | PRESIDENT | 5000.00 | ACCOUNTING | NEW YORK |
|  7844 | TURNER | SALESMAN  | 1500.00 | SALES      | CHICAGO  |
|  7876 | ADAMS  | CLERK     | 1100.00 | RESEARCH   | DALLAS   |
|  7900 | JAMES  | CLERK     |  950.00 | SALES      | CHICAGO  |
|  7902 | FORD   | ANALYST   | 3000.00 | RESEARCH   | DALLAS   |
|  7934 | MILLER | CLERK     | 1300.00 | ACCOUNTING | NEW YORK |
|  NULL | NULL   | NULL      |    NULL | OPERATIONS | BOSTON   |
+-------+--------+-----------+---------+------------+----------+
15 rows in set (0.00 sec)

```
#### 集合运算是一种二目运算符

 一共4种运算符,并，差，交，笛卡尔积

集合运算语法:

 

查询语句
[UNION | UNION ALL | INTERSECT|MINUS |] 
查询语句
...
 

UNION(并集):返回查询结果的全部内容，但是重复内容不显示

UNION ALL(并集):返回查询结果的全部内容，但是重复内容显示

INTERSECT(交集):返回查询结果中的相同部分

MINUS(差集):返回查询结果中的不同部分

 

#### (UNION)并集

```
mysql> SELECT * from dept
    -> union
    -> select * from dept where deptno=10;
+--------+------------+----------+
| deptno | dname      | loc      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
4 rows in set (0.00 sec)
```
结果返回4条记录,重复内容未显示
#### (UNION ALL)并集
```
mysql> SELECT * FROM dept
    -> UNION ALL
    -> SELECT * FROM dept WHERE deptno=10;
+--------+------------+----------+
| deptno | dname      | loc      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
|     10 | ACCOUNTING | NEW YORK |
+--------+------------+----------+
5 rows in set (0.00 sec)
```

结果返回5条记录，重复内容会显示

#### 子查询一单行单列
查询所有比SMITH工资高的人
```
mysql>  SELECT * FROM emp
    ->  WHERE sal>(
    -> SELECT e.sal
    -> FROM emp e
    -> WHERE e.ename='smith');
+-------+--------+-----------+------+---------------------+---------+---------+--------+
| empno | ename  | job       | mgr  | hiredate            | sal     | comm    | deptno |
+-------+--------+-----------+------+---------------------+---------+---------+--------+
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 00:00:00 | 1600.00 |  300.00 |     30 |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 00:00:00 | 1250.00 |  500.00 |     30 |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 |    NULL |     20 |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 00:00:00 | 1250.00 | 1400.00 |     30 |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 |    NULL |     30 |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 |    NULL |     10 |
|  7788 | SCOTT  | ANALYST   | 7566 | 1982-12-09 00:00:00 | 3000.00 |    NULL |     20 |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 |    NULL |     10 |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 00:00:00 | 1500.00 |    0.00 |     30 |
|  7876 | ADAMS  | CLERK     | 7788 | 1983-01-12 00:00:00 | 1100.00 |    NULL |     20 |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 00:00:00 |  950.00 |    NULL |     30 |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 |    NULL |     20 |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 00:00:00 | 1300.00 |    NULL |     10 |
+-------+--------+-----------+------+---------------------+---------+---------+--------+
13 rows in set (0.00 sec)
```


#### 单行多列
查询所有工资和职位和SMITH相同的人
```
mysql> SELECT * FROM emp
    ->  WHERE (sal,job)=(
    -> SELECT sal,job
    -> FROM emp
    -> WHERE ename='smith');
+-------+-------+-------+------+---------------------+--------+------+--------+
| empno | ename | job   | mgr  | hiredate            | sal    | comm | deptno |
+-------+-------+-------+------+---------------------+--------+------+--------+
|  7369 | SMITH | CLERK | 7902 | 1980-12-17 00:00:00 | 800.00 | NULL |     20 |
+-------+-------+-------+------+---------------------+--------+------+--------+
1 row in set (0.00 sec)
```

#### 多行单列 IN ANY,ALL EXISTS
```
mysql> SELECT * FROM emp
    -> WHERE deptno IN(
    -> SELECT deptno
    -> FROM dept);
+-------+--------+-----------+------+---------------------+---------+---------+--------+
| empno | ename  | job       | mgr  | hiredate            | sal     | comm    | deptno |
+-------+--------+-----------+------+---------------------+---------+---------+--------+
|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 00:00:00 |  800.00 |    NULL |     20 |
|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 00:00:00 | 1600.00 |  300.00 |     30 |
|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 00:00:00 | 1250.00 |  500.00 |     30 |
|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 |    NULL |     20 |
|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 00:00:00 | 1250.00 | 1400.00 |     30 |
|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 |    NULL |     30 |
|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 |    NULL |     10 |
|  7788 | SCOTT  | ANALYST   | 7566 | 1982-12-09 00:00:00 | 3000.00 |    NULL |     20 |
|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 |    NULL |     10 |
|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 00:00:00 | 1500.00 |    0.00 |     30 |
|  7876 | ADAMS  | CLERK     | 7788 | 1983-01-12 00:00:00 | 1100.00 |    NULL |     20 |
|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 00:00:00 |  950.00 |    NULL |     30 |
|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 |    NULL |     20 |
|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 00:00:00 | 1300.00 |    NULL |     10 |
+-------+--------+-----------+------+---------------------+---------+---------+--------+
14 rows in set (0.00 sec)
```

#### ANY

* =ANY 功能与IN一样
* >ANY(>=ANY) 比子查询中最小的记录还要大于（大于等于）
* <ANY(<=ANY) 比子查询中最大的记录还要大于（小于等于）
```
mysql> SELECT sal
    -> FROM emp
    -> WHERE job='MANAGER';
+---------+
| sal     |
+---------+
| 2975.00 |
| 2850.00 |
| 2450.00 |
+---------+
3 rows in set (0.00 sec)

```
使用ANY
```
mysql> SELECT ename,sal FROM emp
    -> WHERE sal >=ANY(
    -> SELECT sal
    -> FROM emp
    -> WHERE job='MANAGER');
+-------+---------+
| ename | sal     |
+-------+---------+
| JONES | 2975.00 |
| BLAKE | 2850.00 |
| CLARK | 2450.00 |
| SCOTT | 3000.00 |
| KING  | 5000.00 |
| FORD  | 3000.00 |
+-------+---------+
6 rows in set (0.00 sec)
```
```
mysql> SELECT ename,sal FROM emp
    -> WHERE sal >ANY(
    -> SELECT sal
    -> FROM emp
    -> WHERE job='MANAGER');
+-------+---------+
| ename | sal     |
+-------+---------+
| JONES | 2975.00 |
| BLAKE | 2850.00 |
| SCOTT | 3000.00 |
| KING  | 5000.00 |
| FORD  | 3000.00 |
+-------+---------+
5 rows in set (0.00 sec)
```



#### ALL

* =ALL 功能与IN一样
* >ALL(>=ALL) 比子查询中最大的记录还要大于（大于等于）
* <ALL(<=ALL) 比子查询中最小的记录还要小于（小于等于）

大于ALL
```
mysql> SELECT ename,sal,job FROM emp
    -> WHERE sal >ALL(
    -> SELECT sal
    -> FROM emp
    -> WHERE job='MANAGER');
+-------+---------+-----------+
| ename | sal     | job       |
+-------+---------+-----------+
| SCOTT | 3000.00 | ANALYST   |
| KING  | 5000.00 | PRESIDENT |
| FORD  | 3000.00 | ANALYST   |
+-------+---------+-----------+
3 rows in set (0.00 sec)
```
小于all
```
mysql> SELECT ename,sal,job FROM emp
    -> WHERE sal <ALL(
    -> SELECT sal
    -> FROM emp
    -> WHERE job='MANAGER');
+--------+---------+----------+
| ename  | sal     | job      |
+--------+---------+----------+
| SMITH  |  800.00 | CLERK    |
| ALLEN  | 1600.00 | SALESMAN |
| WARD   | 1250.00 | SALESMAN |
| MARTIN | 1250.00 | SALESMAN |
| TURNER | 1500.00 | SALESMAN |
| ADAMS  | 1100.00 | CLERK    |
| JAMES  |  950.00 | CLERK    |
| MILLER | 1300.00 | CLERK    |
+--------+---------+----------+
8 rows in set (0.00 sec)
```

#### EXISTS的子查询

关键字EXISTS是一个布尔类型，当返回的结果集时为TRUE，不能返回结果集时为FALSE.
查询部门表中有员工的部门或者没有员工部门 多行单列
```
mysql> SELECT *
    -> FROM dept d
    -> WHERE NOT EXISTS(
    -> SELECT * FROM emp
    -> WHERE deptno = d.deptno
    -> );
+--------+------------+--------+
| deptno | dname      | loc    |
+--------+------------+--------+
|     40 | OPERATIONS | BOSTON |
+--------+------------+--------+
1 row in set (0.00 sec)
```

```
mysql> SELECT *
    -> FROM dept d
    -> WHERE  EXISTS(
    -> SELECT * FROM emp
    -> WHERE deptno = d.deptno
    -> );
+--------+------------+----------+
| deptno | dname      | loc      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
+--------+------------+----------+
3 rows in set (0.00 sec)

```

多行多列

查询员工表中各个部门的编辑，名称，地址，员工人数，平均工资

```
mysql> SELECT d.deptno,d.dname,d.loc,COUNT(e.empno) counts,ROUND(AVG(e.sal),2) avgsal
    -> FROM emp e INNER JOIN dept d
    -> ON e.deptno=d.deptno
    -> GROUP BY deptno,dname,loc;
+--------+------------+----------+--------+---------+
| deptno | dname      | loc      | counts | avgsal  |
+--------+------------+----------+--------+---------+
|     10 | ACCOUNTING | NEW YORK |      3 | 2916.67 |
|     20 | RESEARCH   | DALLAS   |      5 | 2175.00 |
|     30 | SALES      | CHICAGO  |      6 | 1566.67 |
+--------+------------+----------+--------+---------+
3 rows in set (0.00 sec)

mysql> SELECT d.deptno,d.dname,d.loc,COUNT(e.empno) counts,ROUND(AVG(e.sal),2) avgsal
    -> FROM emp e INNER JOIN dept d
    -> ON e.deptno=d.deptno
    -> GROUP BY deptno DESC,dname,loc;
+--------+------------+----------+--------+---------+
| deptno | dname      | loc      | counts | avgsal  |
+--------+------------+----------+--------+---------+
|     30 | SALES      | CHICAGO  |      6 | 1566.67 |
|     20 | RESEARCH   | DALLAS   |      5 | 2175.00 |
|     10 | ACCOUNTING | NEW YORK |      3 | 2916.67 |
+--------+------------+----------+--------+---------+
3 rows in set (0.00 sec)
```

查询每个部门的

```
mysql> SELECT
    ->   e.deptno,
    ->   de.dname,
    ->   de.loc,
    ->   de.counts,
    ->   de.avgsal
    -> FROM
    ->   dept e
    ->   INNER JOIN
    ->     (SELECT
    ->       d.deptno,
    ->       d.dname,
    ->       d.loc,
    ->       COUNT(e.empno) counts,
    ->       ROUND(AVG(e.sal), 2) avgsal
    ->     FROM
    ->       emp e
    ->       INNER JOIN dept d
    ->         ON e.deptno = d.deptno
    ->     GROUP BY deptno DESC,
    ->       dname,
    ->       loc) de
    ->     ON e.deptno = de.deptno ;
+--------+------------+----------+--------+---------+
| deptno | dname      | loc      | counts | avgsal  |
+--------+------------+----------+--------+---------+
|     10 | ACCOUNTING | NEW YORK |      3 | 2916.67 |
|     20 | RESEARCH   | DALLAS   |      5 | 2175.00 |
|     30 | SALES      | CHICAGO  |      6 | 1566.67 |
+--------+------------+----------+--------+---------+
3 rows in set (0.00 sec)
```

