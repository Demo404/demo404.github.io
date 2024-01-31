---
title: MySQL学习 ｜ 二、SQL分类以及DDL语句的语法
date: 2023-07-28 10:37:46
tags: [数据库, MySQL]
---

#### **一、SQL的分类**‍

**DDL**（Date Definiition Lanhuage）：数据定义语言，用来定义数据库对象（数据库、表、字段等）。  
 ```
create table 表名;
 ```

**DML**（Data Manipulation Language）：数据操作语言，用来对数据库中的数据进行增删改查。
 ```
alter table 表名 add 字段名 类型(长度);
 ```

**DQL**（Data Query Language）：数据查询语言，用来查询数据库中表里面的数据。
```
select * from 表名;
```

**DCL**（Data Control Language）：数据控住语言，用来创建用户，控制用户对数据库的访问权限。‍
```
grant all privileges on *.* to 'XXX'@'%' identified by 'XXX' with grant option;
```

</br>

#### **二、DDL在数据库上的操作**

查询所有数据库: 
```
SHOW DATABASES;
```
查询当前数据库：  
```
SHOW DATABASE();
```
 创建数据库：  
```
CREATE DATABASE [IF NOT EXISTS] 库名 [DEFAULT CHARSET utf8mb4];
```
 删除数据库：‍‍‍‍‍‍‍‍
```
DROP DATABASE [IF NOT EXISTS] 库名;
```
 使用数据库：
```
USE 库名;
```

</br>

#### **三、DDL在表上操作**

查询当前数据库中的所有表：
```
SHOW TABLES;
```
查询表结构（不会展示字段注释）：  
```
DESC 表名;
```
查询表的创建语句：
```
SHOW CREATE TABLE 表名;
```
 创建表：  
```
create table 表名(
    字段1 字段1类型 [comment 字段1注释],
    字段2 字段2类型 [comment 字段2注释],
    字段3 字段3类型 [comment 字段3注释],
    ......
    字段n 字段n类型 [comment 字段n注释]
)[comment 表注释];
```
 添加字段：
```
ALTER TABLE 表名 ADD 字段名 类型(长度);
```
 修改字段的数据类型：
```
ALTER TABLE 表名 MODIFY 字段名 字段类型(长度);
```
修改字段的名称和类型：‍‍
```
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 字段类型(长度);
```
 删除字段：
```
ALTER TABLE 表名 DROP 字段名;
```
 修改表名称：
```
ALTER TABLE 表名 RENAME TO 新表名;
```
删除表：‍‍‍‍
```
DROP TABLE [IF EXISTS] 表名;TRUNCATE TABLE 表名;
```

DROP：物理删除表结构加数据。‍‍‍‍ 
TRUNCATE：物理删除表结构加数据后 重新创建表。

</br>

#### **四、DDL语句中的数据类型**  
1、数值类型  
|  类型   | 大小  | 描述  |
|  ----  | ----  | ----  |
| TINYINT  | 1 bytes | 小整数值 |
| SMALLINT | 2 bytes | 大整数值 |
| MEDIUMINT  | 3 bytes | 大整数值 |
| INT 或者 INTEGER | 4 bytes | 大整数值 |
| BIGINT | 8 bytes | 极大整数值 |
| FLOAT | 4 bytes | 单精度浮点整数 |
| DOUBLE | 8 bytes | 双精度浮点整数 |
| DECIMAL |  | 小数[精确定点数，依赖于M（精度）和D（标度）] |

2、字符串类型‍‍‍‍‍‍

| 类型       | 大小                 | 描述                         |
| ---------- | -------------------- | ---------------------------- |
| CHAR       | 0 - 255 bytes        | 定长字符串                   |
| VARCHAR    | 0 - 65536 bytes      | 变长字符串                   |
| TINYBLOG   | 0 - 255 bytes        | 不超过255个字符的二进制数据  |
| TINYTEXT   | 0 - 255 bytes        | 短文本字符串                 |
| BLOG       | 0 - 65536 bytes      | 二进制形式的长文本数据       |
| TEXT       | 0 - 65536 bytes      | 长文本数据                   |
| MEDIUMBLOG | 0 - 16777215 bytes   | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0 - 16777215 bytes   | 中等长度文本数据             |
| LONGBLOG   | 0 - 4294967295 bytes | 二进制形式的极大文本数据     |
| LONGTEXT   | 0 - 4294967295 bytes | 极大文本数据                 |

&emsp;&emsp; **char(10):** 即使存储一个字符，剩下的使用空格占位，效率高。
&emsp;&emsp; **varchar(10):** 存储一个字符，实际占用一个字符，效率低一点。   

3、日期时间类型

| 类型      | 大小 | 范围                                       | 格式                | 描述                     |
| --------- | ---- | ------------------------------------------ | ------------------- | ------------------------ |
| DATE      | 3    | 1000-01-01 至 9999-12-31                   | YYYY-MM-DD          | 日期值                   |
| TIME      | 3    | -838:59:59 至 838:59:59                    | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1    | 1901 至 2155                               | YYYY                | 年份值                   |
| DATETIME  | 8    | 1000-01-01 00:00:00 至 9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值         |
| TIMESTAMP | 4    | 1970-01-01 00:00:01 至 2038-01-19 03:14:07 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值，时间戳 |

