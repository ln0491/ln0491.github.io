---
title: mysql学习十五子查询四(在select子句中使用子查询)
date: 2017-02-16 13:22:23
categories: mysql
tags: mysql
---

### mysql学习十五子查询四(在select子句中使用子查询)

#### 查询出每个部门的编号，名称，位置，部门人数，平均工资
使用from 实现
```
mysql> SELECT d.deptno,d.dname,d.loc,tmp.counts,tmp.avgsal
    -> FROM dept d,(
    -> SELECT deptno,COUNT(empno) counts,AVG(sal) avgsal
    -> FROM emp
    -> GROUP BY deptno
    -> ) tmp
    -> WHERE d.deptno=tmp.deptno;
+--------+------------+----------+--------+-------------+
| deptno | dname      | loc      | counts | avgsal      |
+--------+------------+----------+--------+-------------+
|     10 | ACCOUNTING | NEW YORK |      3 | 2916.666667 |
|     20 | RESEARCH   | DALLAS   |      5 | 2175.000000 |
|     30 | SALES      | CHICAGO  |      6 | 1566.666667 |
+--------+------------+----------+--------+-------------+
3 rows in set (0.00 sec)
```
select

```
mysql> SELECT d.deptno,d.dname,d.loc,
    -> (SELECT COUNT(empno) FROM emp WHERE emp.deptno=d.deptno GROUP BY emp.deptno) con,
    -> (SELECT AVG(sal) FROM emp WHERE emp.`deptno`=d.deptno GROUP BY emp.`deptno`) avgsal
    -> FROM dept d;
+--------+------------+----------+------+-------------+
| deptno | dname      | loc      | con  | avgsal      |
+--------+------------+----------+------+-------------+
|     10 | ACCOUNTING | NEW YORK |    3 | 2916.666667 |
|     20 | RESEARCH   | DALLAS   |    5 | 2175.000000 |
|     30 | SALES      | CHICAGO  |    6 | 1566.666667 |
|     40 | OPERATIONS | BOSTON   | NULL |        NULL |
+--------+------------+----------+------+-------------+
4 rows in set (0.00 sec)
```
