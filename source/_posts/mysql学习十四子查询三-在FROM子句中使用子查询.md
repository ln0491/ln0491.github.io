---
title: mysql学习十四子查询三(在FROM子句中使用子查询)
date: 2017-02-16 13:21:49
categories: mysql
tags: mysql
---

### mysql学习十四子查询三(在FROM子句中使用子查询)

FROM子句中使用子查询一般都是返回多行多列，可以将其当作一张数据表


#### 查询出每个部门的编号，名称，位置，部门人数，平均工资
```
mysql> SELECT d.deptno,d.dname,d.loc,dp.counts,dp.avgsal
    -> FROM dept d,(
    -> SELECT deptno,COUNT(empno) counts,AVG(sal) avgsal
    -> FROM emp
    -> GROUP BY deptno
    -> )dp
    -> WHERE d.deptno = dp.deptno;
+--------+------------+----------+--------+-------------+
| deptno | dname      | loc      | counts | avgsal      |
+--------+------------+----------+--------+-------------+
|     10 | ACCOUNTING | NEW YORK |      3 | 2916.666667 |
|     20 | RESEARCH   | DALLAS   |      5 | 2175.000000 |
|     30 | SALES      | CHICAGO  |      6 | 1566.666667 |
+--------+------------+----------+--------+-------------+
3 rows in set (0.00 sec)
```

### 查询出所有在部门SALES(销售部)工作的员工编号，姓名，基本工资，奖金，职位，入职日期，部门最高和最低工资

```
mysql> SELECT e.empno,e.ename,e.sal,e.comm,e.job,e.hiredate,e.deptno,temp.maxsal,temp.minsal
    -> FROM emp e,(
    ->             SELECT deptno dno,MAX(sal) maxsal,MIN(sal) minsal
    ->             FROM emp
    ->             GROUP BY deptno
    ->             ) temp
    -> WHERE e.deptno=(SELECT deptno
    ->                  FROM dept
    ->                  WHERE dname='SALES')
    ->                  AND e.deptno=temp.dno;
+-------+--------+---------+---------+----------+---------------------+--------+---------+--------+
| empno | ename  | sal     | comm    | job      | hiredate            | deptno | maxsal  | minsal |
+-------+--------+---------+---------+----------+---------------------+--------+---------+--------+
|  7499 | ALLEN  | 1600.00 |  300.00 | SALESMAN | 1981-02-20 00:00:00 |     30 | 2850.00 | 950.00 |
|  7521 | WARD   | 1250.00 |  500.00 | SALESMAN | 1981-02-22 00:00:00 |     30 | 2850.00 | 950.00 |
|  7654 | MARTIN | 1250.00 | 1400.00 | SALESMAN | 1981-09-28 00:00:00 |     30 | 2850.00 | 950.00 |
|  7698 | BLAKE  | 2850.00 |    NULL | MANAGER  | 1981-05-01 00:00:00 |     30 | 2850.00 | 950.00 |
|  7844 | TURNER | 1500.00 |    0.00 | SALESMAN | 1981-09-08 00:00:00 |     30 | 2850.00 | 950.00 |
|  7900 | JAMES  |  950.00 |    NULL | CLERK    | 1981-12-03 00:00:00 |     30 | 2850.00 | 950.00 |
+-------+--------+---------+---------+----------+---------------------+--------+---------+--------+
6 rows in set (0.00 sec)
```

#### 列出工资比ALLEN或者CLARK多的所有员工的编号，姓名，基本工资，部门名称，领导姓名，部门人数

```
mysql> SELECT e.empno,e.ename,e.sal,d.dname,m.ename,temp.con
    -> FROM emp e,dept d,emp m,(
    ->                           SELECT deptno dno,COUNT(empno) con
    ->                           FROM emp
    ->                           GROUP BY deptno
    ->                           )temp
    -> WHERE e.sal>ANY(SELECT sal
    ->                  FROM emp
    ->                  WHERE ename IN('ALLEN','CLARK')
    ->                  )
    ->               AND e.ename NOT IN ('ALLEN','CLARK')
    ->               AND e.deptno=d.deptno
    ->               AND e.mgr=m.empno
    ->               AND e.deptno=temp.dno;
+-------+-------+---------+----------+-------+-----+
| empno | ename | sal     | dname    | ename | con |
+-------+-------+---------+----------+-------+-----+
|  7788 | SCOTT | 3000.00 | RESEARCH | JONES |   5 |
|  7902 | FORD  | 3000.00 | RESEARCH | JONES |   5 |
|  7566 | JONES | 2975.00 | RESEARCH | KING  |   5 |
|  7698 | BLAKE | 2850.00 | SALES    | KING  |   6 |
+-------+-------+---------+----------+-------+-----+
4 rows in set (0.00 sec)
```

#### 列出公司各个部门的经理（一个部门只有一个）的姓名,工资，部门名称，部门人数，部门平均工资
```
mysql> SELECT e.ename,e.sal,d.deptno,d.dname,tmp.counts,tmp.avgsal,e.job
    -> FROM emp e,dept d,(
    -> SELECT deptno,COUNT(empno) counts,AVG(sal) avgsal
    -> FROM emp
    -> GROUP BY deptno
    -> )tmp
    -> WHERE e.deptno=d.deptno
    -> AND d.deptno=tmp.deptno
    -> AND e.job='manager';
+-------+---------+--------+------------+--------+-------------+---------+
| ename | sal     | deptno | dname      | counts | avgsal      | job     |
+-------+---------+--------+------------+--------+-------------+---------+
| CLARK | 2450.00 |     10 | ACCOUNTING |      3 | 2916.666667 | MANAGER |
| JONES | 2975.00 |     20 | RESEARCH   |      5 | 2175.000000 | MANAGER |
| BLAKE | 2850.00 |     30 | SALES      |      6 | 1566.666667 | MANAGER |
+-------+---------+--------+------------+--------+-------------+---------+
3 rows in set (0.00 sec)
```

