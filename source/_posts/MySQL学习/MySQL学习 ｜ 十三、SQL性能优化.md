---
title: MySQL学习 ｜ 十三、SQL性能优化
date: 2023-08-25 15:11:23
tags: [数据库, MySQL]
---

##### 一、插入数据优化
**批量插入**
单条数据频繁的插入会存在大量的IO和不停的与数据库建立连接。
批量插入的数据建议在500～1000条，更多的话建议使用多个批量插入。
**手动提交事物**
MySQL默认的是自动提交事物，多条插入语句时会频繁的开启、提交事物。
> start transaction;
> insert into tb_user values(1, 'tom'),(2, 'jerry'),(3, 'lacy');
> insert into tb_user values(1, 'tom'),(2, 'jerry'),(3, 'lacy');
> insert into tb_user values(1, 'tom'),(2, 'jerry'),(3, 'lacy');
> commit;

**主键按顺序插入**

> 主键乱序插入 8 2 5 98 2 56 121 1 4 22
> 主键顺序插入 2 3 7 23 46 144 145 400

**大批量数据插入**
如果一次性需要插入大量的数据，使用insert语句插入数据性能较低，此时可以使用MySQL数据库提供的`load`指令进行插入。

> #客户端连接服务器端时，加上参数 --local-infile
> mysql --local-infile -u root -p
> #设置全局参数local_infile为1，开启从本地加载文件导入数据的开关。
> set global local_infile = 1;
> #执行load指令将准备好的数据加载到表结构中
> load data local infile '数据文件路径' into table `表名` fields terminated by ',' lines terminated by '\n';


##### 二、主键优化

**数据组织方式**
在innoDB存储引擎中，表中数据都是根据主键顺序存放的，这种存储方式的表称为索引组织表（index orgnized tab）IOT。
**页合并**
当删除一行数据时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用。当页中删除的纪录达到MERGE_THRESHOLD（默认为页的50%），InnoDB就开始寻找最靠近的页（前或后）看看是否可以讲两个页合并以优化空间的使用。
*MERGE_THRESHOLD*：合并页的阈值，可以自己设置，在创建表或者创建索引的时候指定。
**主键设计原则**
1、满足业务需求的情况下，尽量降低主键的长度，因为二级索引存储的数据就是主键。
2、插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键。
3、尽量不要使用UUID（会产生页分裂以及UUID长度较大，长度较大时加大了二级索引的大小，降低了查询效率）或其他自然主键，如身份证号。
4、业务操作时避免对主键的修改。


##### 三、order by 优化

**两种排序方式：**

- Useing filesort：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区`sort buffer`中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫FileSort排序。

- Useing index：通过有序索引顺序扫描直接返回有序数据，这种情况即为useing index，不需要额外排序，操作效率高。

执行如下SQL：`select id, age, phone from tb_user order by age, phone;`，会产生FileSort排序。对表tb_user创建如下索引：`create index inx_user_age_phone on tb_user(age, phone);`，再次执行上述查询，FileSort消失，转而使用索引排序，提高了查询效率。

紧接着再次执行如下查询：`select id, age, phone from tb_user order by age asc, phone desc;`，会发现分别使用了索引排序和文件内排序，这是因为创建索引时默认字段为升序排列，再创建一个索引`create index inx_user_age_phone on tb_user(age asc, phone desc);`，执行查询语句：`select id, age, phone from tb_user order by age asc, phone desc;`，会发现使用了索引。

**Order By优化原则**

1、根据排序字段建立合适的索引，多字段排序时也遵循最左前缀法则。

2、尽量使用覆盖索引。

3、多个字段排序，有的升序有的降序，此时需要注意联合索引在创建时的规则（ASC/DESC）。

4、如果避免不了FileSort，大数据量排序时，可以适当增加排序缓冲区的大小sort_buffer_size(默认256k)。

```sql
SHOW VARIABLES LIKE 'sort_buffer_size';
```


##### 四、group by 优化

1、分组操作时可以通过索引来提高效率。

2、分组操作时，索引的使用也得满足最左前缀法则。

##### 五、limit优化

一个常见有非常头疼的问题是limit 2000000,10，此时MySQL排序前2000010条记录，仅仅返回2000000～2000010的记录，其他的记录则舍弃，查询时排序代价非常大。

优化思路：一般分页查询时，通过创建覆盖索引能够比较好的提高性能，可以通过覆盖索引加子查询形式进行优化。

```sql
explain select * from tb_sku s, (select id from tb_sku order by id limit 2000000,10) a where a.id = s.id;
```


##### 六、count优化

```sql
explain select count(*) from tb_user;
```

- MyISAM引擎把一个表的总行数存放在磁盘上，因此执行count(*)的时候会直接返回这个数，效率很高。
- InnoDB引擎执行count(*)的时候，需要把数据一行一行的从引擎里面读取出来，然后累计计数。

**优化思路：**目前针对count并没有特别好的优化方式，可以自己在内存中维护一个计数器。

**count的几种用法：**

- count(主键)

  InnoDB引擎会遍历整张表，把每一行的主键id都取出来返回给服务层，服务层拿到主键后，直接按行进行累加（主键不会为null）。

- count(字段)

  没有not null 约束：InnoDB引擎会遍历整张表的每一行数据然后返回给服务层，服务层再判断是否为null，不为null时计数加1。

  有not null约束：InnoDB引擎会遍历整张表的每一行数据然后返回给服务层，服务层进行按行累加。

- count(1)

  InnoDB遍历整张表但不取值，服务层对于返回的每一行都放一个数字“1”进去，直接按行进行累加。

- count(*)

  InnoDB并不会把全部字段取出来，而是专门做了优化，服务层直接按行进行累加。

按照效率排序：**count(*) ≈ count(1) > count(主键) > count(字段)** ，尽量使用count(*)。


##### 七、update优化（避免行锁升级为表锁）

```
update tb_user set name = 'zhang' where id = 1;
```

此时为行锁，对并发无影响。

```
update tb_user set name = 'li' where name = 'zhang';
```

如果name字段无有效的索引，此时将会把整个表锁住。

<font color='red'>InnoDB的行锁是针对索引加的锁（索引不能失效），不是针对记录加的锁，索引失效或者无索引的情况下会将行锁升级为表锁。</font>

update尽量使用主键进行更新。