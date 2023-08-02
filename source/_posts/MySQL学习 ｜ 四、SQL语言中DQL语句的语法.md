---
title: MySQL学习 ｜ 四、SQL语言中DQL语句的语法
date: 2023-07-28 13:53:52
tags: [数据库, MySQL]
---

**DQL：数据查询语言，用来查询数据库中表里面的数据。**

DQL语法‍‍‍


```
SELECT 字段列表 
FROM 表名 
WHERE 条件 
GROUP BY 分组 
HAVING 分组后的条件列表 
ORDER BY 排序字段列表 
LIMIT 分页参数;
```

数据查询语言的分类：

&emsp;&emsp;&emsp;&emsp;基本查询‍‍

&emsp;&emsp;&emsp;&emsp;条件查询（WHERE）

&emsp;&emsp;&emsp;&emsp;聚合查询（COUNT、MAX、MIN、AVG、SUM）

&emsp;&emsp;&emsp;&emsp;分组查询（GROUP BY）‍‍‍‍‍

&emsp;&emsp;&emsp;&emsp;排序查询（ORDER BY）

&emsp;&emsp;&emsp;&emsp;分页查询（LIMIT）

</br>

#### **一、DQL的基础查询**

1、查询返回多个字段


```
SELECT 字段1, 字段2, 字段n FROM 表名;
SELECT * FROM 表名;
```

`select *` 可读性以及效率偏低，推荐 `select 字段` 的方式。

2、设置别名


```
SELECT 字段1 AS [别名1], 字段2 AS [别名2], ... 字段n AS [别名n] FROM 表名;
```

3、去除重复记录


```
SELECT DISTINCT 字段名 FROM 表名；
```

</br>

#### **二、DQL语句的条件查询**‍‍

| 比较运算符       | 功能                             |
| ---------------- | -------------------------------- |
| >, >=            | 大于，大于等于                   |
| <, <=            | 小于，小于等于                   |
| =                | 等于                             |
| <>, !=           | 不等于                           |
| BETWEEN...AND... | 在某个范围内（含最大值和最小值） |
| IN(...)          | 在IN之后的列表中                 |
| LIKE 占位符      | 模糊匹配                         |
| IS NULL          | 是NULL                           |

</br>

| 逻辑运算符 | 功能                     |
| ---------- | ------------------------ |
| AND 或 &&  | 并且（多个条件同时成立） |
| OR 或 \|\| | 或者（多个条件满足一个） |
| NOT 或 ！  | 非，不是                 |



1、查询年龄小于18的员工


```
SELECT * FROM 表名 WHERE age < 18;
```

2、查询年龄等于20的员工‍


```
SELECT * FROM 表名 WHERE age = 20;
```

2、查询年龄不等于20的员工


```
SELECT * FROM 表名 WHERE age != 20;SELECT * FROM 表名 WHERE age <> 20;
```

3、查询没有年龄的员工信息  


```
SELECT * FROM 表名 WHERE age is null;
```

4、查询有年龄的员工信息  


```
SELECT * FROM 表名 WHERE age is not null;
```

6、查询年龄在20到30的员工信息


```
SELECT * FROM 表名 WHERE age between 20 and 30;
SELECT * FROM 表名 WHERE age >= 20 and and <= 30;
```

7、查询年龄为20且性别为女的员工信息‍


```
SELECT * FROM 表名 WHERE age = 20 and sex = '女';
```

8、查询年龄为20或者30或者40的员工信息


```
SELECT * FROM 表名 WHERE age = 20 or age = 30 or age = 40;
SELECT * FROM 表名 WHERE age in (20, 30, 40);
```

9、查询姓名为两个字的员工(一个下划线代表一个字符)


```
SELECT * FROM 表名 WHERE name like '__';
```

10、查询身份证号最后一位是X的员工信息‍‍‍‍


```
SELECT * FROM 表名 WHERE id_card like '%X';
```

  </br>

#### **三、DQL语句的聚合函数‍‍‍‍‍‍**

**聚合函数作用**：将一列数据作为一个整体进行纵向计算。

**注意**：所有的null值不进行聚合函数计算。

1、统计员工数量


```
SELECT count(*) FROM 表名;SELECT count(字段名) FROM 表名;
```

2、统计平均值


```
SELECT avg(字段名) FROM 表名;
```

3、查询最大值  


```
SELECT max(字段名) FROM 表名;
```

4、查询最小值‍


```
SELECT min(字段名) FROM 表名;
```

5、查询某个字段的和


```
SELECT sum(字段名)  FROM 表名;
```

  </br>

#### **四、DQL语句的分组查询‍‍‍‍**

**语法**：`SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY [HAVING 分组后的过滤条件]‍‍‍‍‍`

**注意**：

&emsp;&emsp;&emsp;&emsp;1、分组查询的顺序为where -> 聚合函数 -> having‍‍‍‍‍‍‍‍‍‍‍‍

&emsp;&emsp;&emsp;&emsp;2、分组之后查询的字段一般为聚合函数和分组字段，查询其他字段无意义。

&emsp;&emsp;&emsp;&emsp;1、根据性别分组，查询男女员工的数量


```
SELECT count(*) FROM 表名 GROUP BY gender;
```

2、根据性别分组，查询男女员工的平均年龄  


```
SELECT avg(age) FROM 表名 GROUP BY gender;
```

3、查询年龄小于45的员工并且根据工作地址分组，获取员工数量大于3的工作地址


```
SELECT count(*) as address_count FROM 表名 
WHERE age < 45 
GROUP BY work_address 
HAVING address_count >= 3;
```

#### **五、DQL语句的排序查询**

SQL语句中支持多个字段排序，排序方式有两种：`ASC`代表升序(默认值)，`DESC`代表降序。‍‍

1、根据年龄对员工进行升序排列


```
SELECT 字段名 FROM 表名 ORDER BY age ASC;
```

2、根据入职时间进行降序排列  


```
SELECT 字段名 FROM 表名 ORDER BY entry_date DESC;
```

3、根据年龄对员工进行升序排序，年龄相同根据入职时间将序排列‍‍


```
SELECT 字段名 FROM 表名 ORDER BY age ASC, entry_date DESC;
```

  </br>

#### **六、DQL语句的分页查询**

**语法**：`SELECT 字段名 FROM 表名 LIMIT 起始索引,查询记录数;`

**注意**：`LIMIT`为MySQL的方言，不同数据库分页有不同的实现。‍‍

1、查询第一页的员工数据，每页展示10条

```
SELECT * FROM LIMIT 0,10;
```

2、查询第二页的员工数据，每页展示10条  


```
SELECT * FROM LIMIT 10,10;
```

  </br>

#### **七、DQL语句的执行顺序‍‍‍‍**

```
FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY -> LIMIT
```
