---
title: MySQL学习 ｜ 十二、SQL性能分析和索引使用原则
date: 2023-08-07 07:37:58
tags: [数据库, MySQL]
---

#### 一、查看SQL的执行频率

通过SQL的执行频率可以确定当前数据库执行哪类操作比较多，然后做出对应的优化措施。

通过如下命令可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT等操作的执行频次。

```
SHOW GLOBAL STATUS LIKE 'Com_______'
```

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%BA%8C/%E6%88%AA%E5%B1%8F2023-08-07%2007.47.21.png?e=1691366143&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:BnUqlCMw7NIDASAk6F2A-ie_nbc=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%BA%8C/%E6%88%AA%E5%B1%8F2023-08-07%2007.47.21.png?e=1691366143&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:BnUqlCMw7NIDASAk6F2A-ie_nbc=)

</br>

#### 二、慢查询日志

慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有SQL语句的日志。

MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：

```shell
# 开启MySQL的慢日志查询开关
slow_query_log=1

# 设置慢日志的查询时间，SQL语句执行时间超过指定的值(如下超过10秒则为慢查询)，则会视为慢查询，记录慢查询日志。
long_query_time=10
```

配置完毕以后需重启MySQL服务，慢日志信息记录在文件：`/var/lib/mysql/localhost-show.log`中。



查询慢日志查询是否开启，如下命令：

```sql
SHOW VARIABLES like 'slow_query_log';
```

输出Value值为：`ON` 则说明慢日志查询开启。

</br>

#### 三、show profiles命令

在实际的业务中，有些SQL查询比较简单，但是查询占用的时间刚好小于慢日志记录设置的时间，导致这类慢SQL不能被捕捉到。

show profiles 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。通过`have_profiling`参数，能够查看到当前MySQL是否支持profile操纵。

```sql
SELECT @@have_profiling;
```

输出值为：`YES`则说明当前数据库支持profile。

默认profile是关闭的，可以通过命令开启GLOBAL或者SESSION级别的profiling。

```sql
SET [GLOBAL | SESSION] profiling = 1;
```

profile相关的一些命令

```sql
# 查看每一条SQL的耗时基本情况
show profiles;

# 查看指定query的SQL语句的各个阶段的耗时情况,query_id:上一步的id
show profile for query [query_id];

# 查看指定SQL的CPU占用情况
show profile cpu for query [query_id];
```



#### 四、explain命令

`EXPLAIN`或者`DESC`命令用来获取MySQL是如何执行SELECT语句的信息，包括SELECT语句执行过程中表如何连接和连接顺序等信息。

**语法**

```SQL
# 直接在select语句前面加上关键字 explain/desc
EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件;
```

EXPLAIN展示的各个字段的含义：

| 字段名       | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| id           | 查询的序列号，表示查询中执行select字句或者是操作表的顺序。（id越大越先被执行，id相同时按照从上往下的顺序执行查询） |
| select_type  | 表示select的类型，常见的取值有SIMPLE（简单表，即不使用表链接或者子查询）、PRIMARY（主查询，即外层查询）、UNION（UNION中的第二个或者后面的查询）、SUBQUERY（SELECT/WHERE之后包含了子查询）等。 |
| type         | 表示连接类型，性能由好到差为NULL、SYSTEM、CONST、EQ_REF、REF、RANGE、INDEX、ALL。 |
| possible_key | 显示可能用在这张表上的索引，一个或者多个。                   |
| key          | 实际使用的索引，没有则为NULL。                               |
| key_length   | 表示索引使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精度的情况下，长度越短越好。 |
| rows         | MySQL认为必须要执行查询的行数，在InnoDB引擎中是一个估算值，可能并不总是准确的。 |
| filtered     | 表示返回结果的行数占需要读取行数的百分比，filtered的值越大越好。 |
| extra        | 额外信息                                                     |

extra中显示的信息，MySQL版本不通则显示的不同。



#### 五、索引的使用规则以及失效场景

**最左前缀法则：**如果索引了多列（联合索引），要遵守最佳左前缀法则。最左前缀法则是指查询从索引的最左列开始，并且不跳过索引中的列。如果跳过了索引中的某一列，索引将部分失效（跳过列后方的字段索引失效）；

> where条件后面列的顺序可以和索引中定义的顺序不一致。

**SQL提示：**是优化数据库的一个重要手段，简单来说就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

> use index;  告诉数据库你应该使用哪一个索引（建议使用）
>
> SELECT 字段列表 FROM 表名 use index(index_name) WHERE 条件;

> ignore index;  告诉数据库你不要使用哪一个索引
>
> SELECT 字段列表 FROM 表名 ignore index(index_name) WHERE 条件;

> force index;  告诉数据库你必须要使用哪一个索引（强制使用）
>
> SELECT 字段列表 FROM 表名 force index(index_name) WHERE 条件;

**覆盖索引：**尽量使用覆盖索引（查询使用了索引，并且查询的字段全部可以在索引中找到），避免使用SELECT *（极易出现回表查询）。

- 现有如下表结构，分别对主键ID和字段name建立索引：

| ID   | NAME | GENDER | CREATEDATE |
| ---- | ---- | ------ | ---------- |
| 2    | Arm  | 1      | 2023-08-22 |
| 4    | Lily | 1      | 2023-08-01 |
| 5    | Rosy | 0      | 2023-03-10 |
| 8    | Zoom | 1      | 2023-11-09 |

- 聚集索引（id）：

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%BA%8C/%E6%88%AA%E5%B1%8F2023-08-21%2008.19.33.png](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%BA%8C/%E6%88%AA%E5%B1%8F2023-08-21%2008.19.33.png)

- 辅助索引（name）：

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%BA%8C/%E6%88%AA%E5%B1%8F2023-08-21%2008.19.56.png](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%BA%8C/%E6%88%AA%E5%B1%8F2023-08-21%2008.19.56.png)

- 分别进行以下查询：

```sql
SELECT * FROM tb_stu WHERE id = 2;

走聚集索引，一次就可查询出数据。

SELECT id, name FROM tb_stu WHERE name = 'Arm';

走辅助索引即可查询出需要的所有数据（覆盖索引）。

SELECT id, name, gender FROM tb_stu WHERE name = 'Arm';

首先根据辅助索引查询，只能查询出name和id字段，然后再使用id字段去聚集索引中查询gender字段（回表查询）。
```

> **思考：**一张表有四个字段（id, name, password, status），由于数据量较大需要对以下SQL进行优化，该如何进行才是最优方案？
>
> ```sql
> SELECT id, name, password, status FROM tb_user WHERE name = 'Tom';
> ```
>
> *方案1:* 为字段name建立索引。
>
> 如果只为name字段建立索引的话，查询回进行两次，首先走辅助索引查询出name和id，然后再使用主键索引根据id查询password和status字段，涉及到了回表操作。
>
> *方案2:* 为字段name和password以及status字段建立索引。
>
> 此时where条件会使用建立的索引（最佳左前缀），并且根据索引就能查询出需要的数据，不需要回表等操作，因此性能最高。

**前缀索引：**当字段类型为字符串（varchar，text）时，有时候需要索引很长的字符串（比如博客中的内容），只会让索引变得很大，查询时会浪费大量的磁盘IO，影响查询效率。此时可以只将字符串的一部分前缀建立索引，这样可以大大减少索引空间进而提示查询效率。

语法：

```sql
CREATE INDEX index_name ON table_name(column(n));	-- n:前缀长度
```

如何决定前缀的长度：可以根据前缀的选择性来决定，而前缀的选择性是指不重复的索引值（基数）和数据总量的比值，选择性越高则查询效率越高，唯一索引的选择性为1，是最好的选择性，性能也是最高的。

```sql
选择行计算：
SELECT count (distinct email)/count(*) FROM tb_user;	                -- 1
SELECT count (distinct substring(email,1,4))/ count(*) FROM tb_user;	-- 0.9876
```

**范围查询：**联合索引中，出现范围查询（>, <）时，范围查询右侧的索引将失效。

> 大于等于, 小于等于 不会使得索引失效，因此在业务允许的情况下尽量使用大于等于代替大于，用小于等于代替小于。

**索引列上运算：**在索引列上进行计算操作将导致索引失效。

**字符串不加引号：**使用字符串列时不加引号会导致索引失效（隐式转换）。

**模糊查询：**如果仅是尾部进行模糊匹配索引不会失效，如果是头部进行模糊匹配则会失效。

> WHERE name like '%张三'；	索引失效
>
> WHERE name like '张三%'；	索引有效

**OR连接的条件：**用or分隔开的条件，如果or前面的条件中的列有索引，而后面的列中没有索引，那么涉及到的索引都不会生效。or前后的条件都有索引时会生效。

**数据分布影响：**如果MySQL评估使用索引比全表更慢时则不会使用索引。

如果使用索引查出的数据量大于全表的数据量的1/2，MySQL会使用全表扫描。

> 对字段使用not null或者is not null时，MySQL也会评估是走索引还是全表扫描。

</br>

#### 六、索引的设计规则

1.  针对数据量较大且查询比较频繁的表建立索引。

2. 针对常作为查询条件（where）、排序（order）、分组（group by）操作的字段建立索引。

3. 尽量选择区分度较大的列作为索引，尽量选择唯一索引，区分度越高，使用索引的效率越高。

4. 如果是字符串类型的字段并且字段的长度较长，可以针对字段的特点建立前缀索引。

5. 尽量使用联合索引，减少单列索引，查询时联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。

6. 控制索引的数量，索引并不是越多越好，索引越多，维护索引的成本就越高，增删改的效率也会被影响。

7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好的确定哪一个索引可最有效的用于查询。
   </br>

   
