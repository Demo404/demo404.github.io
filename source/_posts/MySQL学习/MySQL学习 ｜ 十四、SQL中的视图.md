---
title: MySQL学习 ｜ 十四、SQL中的视图
date: 2023-08-28 08:31:38
tags: [数据库, MySQL]
---
##### 一、视图的基本介绍和语法
**定义**
视图(View): 是一种虚拟存在的表，视图中的数据并不是真实存在的，行和列数据来自定义视图的查询中使用的表，并且是在使用时图时动态生成。
通俗讲，视图只保存了查询数据的SQL，不保存查询结果，所以我们创建视图的时候主要工作就落在了创建这条SQL查询语句上。

**语法**
```sql
创建视图
CRAETE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION];
```
```sql
查看创建视图语句
SHOW CREATE VIEW 视图名称;
查看视图数据
SELECT 字段列表 FROM 视图名称 [WHERE ...];
```
```sql
修改视图
方式一：CRAETE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION];
视图二：ALTER 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION];
```
```sql
删除视图
DROP VIEW [IF EXISTS] 视图名称, [视图名称,]...
```
</br>

##### 二、视图中的检查选项
当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查每个正在更改的行，例如插入，更新，删除，已使其符合视图的定义。MySQl允许基于另一个视图创建视图，它会依赖检查视图中的规则以保持一致性。
**WITH CASCADED CHECK OPTION   检查选项会传递**
需要满足当前视图的条件。并且对于所有底部视图的条件，也需要一并满足，哪怕底部视图没有定义with check option语句。
> *不指定约束类型时，默认为cascaded约束，with check option = with cascaded check option。*

创建视图 
```sql
create or replace view stu_v_1 as select id, name from student where id >= 10;
```
执行以下插入语句均不会报错，因为当前视图没有检查选项。
```sql
insert into stu_v_1 values(8, 'tom');
insert into stu_v_1 values(18, 'jerry');
```
重新创建视图，指定约束条件
```sql
create or replcae view stu_v_2 as select id, name from stu_v_1 where id <= 20 with cascaded check option;
```
再次执行以下插入语句，第一条语句会报错，因为stu_v_1要求id>=10,但是第二条语句不会报错。
```sql
insert into stu_v_2 values(8, 'tom');
insert into stu_v_2 values(18, 'jerry');
```
**WITH LOCAL CHECK OPTION    检查选项不会传递**
需要满足当前视图的条件。对于底部视图，也是先看是否有指定的with check option语句，有的话对应处理，无则不需要满足底部视图的条件。
创建视图 
```sql
create or replcae view stu_v_3 as select id, name from student where id >= 10;
```
执行以下插入语句均不会报错，因为当前视图没有检查选项。
```sql
insert into stu_v_1 values(8, 'tom');
insert into stu_v_1 values(18, 'jerry');
```
重新创建视图，指定约束条件
```sql
create or replcae view stu_v_4 as select id, name from stu_v_3 where id <= 20 with local check option;
```
再次执行以下插入语句，都不会报错，因为stu_v_3上没有check option校验，它只会校验stu_v_4上的约束满足即可。
```sql
insert into stu_v_2 values(8, 'tom');
insert into stu_v_2 values(18, 'jerry');
```
</br>

##### 三、视图的更新和作用
**视图的更新**
要使视图可以更新，视图中的行与基础表中的行之间必须存在一一对应的关系。如果视图包含以下任意一项，则该视图不可更新：
    1、聚合函数或者窗口函数（SUM(), MIN(), MAX(), COUNT()等）
    2、DISTINCT
    3、GROUP BY
    4、HAVING
    5、UNION或者UNION ALL
**视图的作用**
*简单：*视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些经常被使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次都指定全部的条件。
*安全：*数据库可以授权，但是不能授权到数据库特定的行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据。
*数据独立：*视图可以帮助用户屏蔽真实表结构变化带来的影响。