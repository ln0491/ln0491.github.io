---
title: mysql学习十三子查询二(在HAVING子句中使用子查询)
date: 2017-02-16 13:21:30
categories: mysql
tags: mysql
---

### mysql学习十三子查询二(在HAVING子句中使用子查询)

HAVING子句的主要功能是对分组后的数据进行过滤，如果子查询在HAVING中表示要进行分组过滤，一般返回单行单列的数据


#### 查询部门编号，人数，平均工资，并且要求这些部门的平均工资高于公司的平均工资

```
mysql> SELECT deptno,COUNT(empno),ROUND(AVG(sal),2)
    -> FROM emp
    -> GROUP BY deptno
    -> HAVING AVG(sal)>(
    -> SELECT AVG(sal)
    -> FROM emp);
+--------+--------------+-------------------+
| deptno | COUNT(empno) | ROUND(AVG(sal),2) |
+--------+--------------+-------------------+
|     10 |            3 |           2916.67 |
|     20 |            5 |           2175.00 |
+--------+--------------+-------------------+
2 rows in set (0.00 sec)

```
