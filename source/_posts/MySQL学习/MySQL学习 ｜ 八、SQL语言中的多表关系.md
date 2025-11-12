---
title: MySQL学习 ｜ 八、SQL语言中的多表关系
date: 2023-08-02 16:51:03
tags: [数据库, MySQL]
---

**笛卡尔积：**笛卡尔积是指在数学中，两个集合A集合和B集合的所有组合情况（多表查询时需要消除无效的笛卡尔积）。



#### **多表查询的分类**

1、内连接（C表示部分）

![http://jd.goudan.ltd:9000/hexo-blog/MySQL学习七/交集.png](http://jd.goudan.ltd:9000/hexo-blog/MySQL学习七/交集.png)

```
隐式内连接    
SELECT * FROM 表1, 表2 WHERE 条件;

显示内连接    
SELECT * FROM 表1 [INNER]JOIN 表2 ON 条件;
```

2、外连接

![http://jd.goudan.ltd:9000/hexo-blog/MySQL学习七/交集.png](http://jd.goudan.ltd:9000/hexo-blog/MySQL学习七/交集.png)

左外连接表示A和C部分

```
SELECT 字段 FROM 表1 LEFT [OUTER] JOIN 表2 ON 条件;
```

右外连接表示B和C部分  

```
SELECT 字段 FROM 表1 RIGHT [OUTER] JOIN 表2 ON 条件;
```

3、自连接

```
SELECT 字段 FROM 表A 别名A JOIN 表A 别名B ON 条件;
```

4、联合查询‍

    多次查询的结果合并起来，形成一个新的查询结果集。多张表的列数和类型需要保持一致。

```
SELECT 字段列表 FROM 表A...UNION [ALL] SELECT 字段列表 FROM 表B...
```

UNION: 查询结果去重‍

UNION ALL: 查询结果不会去重‍‍‍‍‍‍‍‍‍

4、子查询

SQL语句中嵌套SELECT语句，称为嵌套语句，又称子查询。‍‍‍‍

```
SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
```