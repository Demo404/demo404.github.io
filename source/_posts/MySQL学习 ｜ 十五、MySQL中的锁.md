---
title: MySQL学习 ｜ 十五、MySQL中的锁
date: 2023-08-30 08:14:28
tags: [数据库, MySQL]
---

##### 一、锁的基本介绍
**介绍**
锁是计算机协调多个进程或者线程并发访问某一资源的机制，在数据库中，除了传统的计算资源（CPU、RAM、IO）争用外，数据也是一种供多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。
**分类**
MySQL中的锁，按照锁的粒度分为以下三类：
1.全局锁：锁定数据库中的所有表
2.表级锁：每次操作锁住整张表
3.行级锁：每次操作锁住对应的行

</br>

##### 二、全局锁
**介绍**
全局锁就是对整个数据库实例开锁，加锁后整个实例处于只读状态，后续的DML的写语句，DDL语句，已经更新的操作的事物的提交语句都将被阻塞。
最典型的使用场景就是全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。
**语法**
创建全局锁
```sql
flush tables with read lock;
```
解除全局锁
```sql
unloak tables;
```
数据库实例备份
```sql
mysqldump -h 主机地址 -u用户名 -p密码 数据库实例名 > 备份文件.sql
```
**特点**
数据库中的全局锁是一个比较重的操作，会存在以下问题：
1.如果在主库上执行备份，那么备份期间都不能执行更新，整个业务停摆。
2.如果在从库上备份，那么备份期间从库不能执行主库同步过来的二进制日志（binlog），导致主从延迟。
在InnoDB引擎中，我们可以在备份时加行参数`--single-transaction`参数来完成不加锁的一致性数据备份。
```sql
mysqldump --single-transaction -h 主机地址 -u用户名 -p密码 数据库实例名 > 备份文件.sql
```

##### 三、表级锁
**介绍**
表级锁，每次操作都会锁住整张表。锁定力度大，发生锁冲突的概率高，并发度支持较低。应用在MyISAM，InnoDB，BDB等存储引擎中。
对于表级锁，主要分为一下三类：
	1.表锁
	2.元数据锁（Meta Data Locl）MDL
	3.意向锁
**表锁**
对于表锁，分为两类
	1.表共享读锁（read lock）
*session1对表A加read lock，此时session1可以对表A执行查询操作但是不能执行修改操作，session2可以对表A执行查询操作但是不能执行修改操作。*
	2.表独占写锁（write lock）
*session1对表A加write lock，此时session1可以对表A执行查询操作和修改操作，session2即不能对表A执行查询操作页不能执行修改操作。*
语法
1.加锁 `lock tables 表名... read/write`
2.解锁 `unlock tables / 断开客户端连接`
特点
读锁不回阻塞客户端的读操作，但是会阻塞写操作。写锁会阻塞其他客户端的读写但不回阻塞当前加锁客户端的读写。
**元数据锁**
MDL加锁过程是系统自动控制的，无需显示调用，在访问一张表的时候会自动加上，MDL锁的作用是维护元数据的数据一致性，在表上有活动事物的时候，不可以对元数据进行写入操作。<font color="red">为了避免DML和DDL的冲突，保证读写的正确性。</font>
在MySQL5.5中引入了MDL，当对一张表进行增删改操作的时候，加MDL读锁（共享）；当对一张表进行结构变更操作的时候，加MDL写锁（排他）。

| 对应SQL                                       | 锁类型                               | 说明                                           |
| --------------------------------------------- | ------------------------------------ | ---------------------------------------------- |
| lock table xxx read/write                     | SHARE_READ_ONLY、SHARE_NO_READ_WRITE |                                                |
| select、select ... lock in share mode         | SHARE_READ                           | 与SHARE_READ、SHARE_WRITE兼容，与EXCLUSIVE互斥 |
| Insert、update、delete、select ... for update | SHARE_WRITE                          | 与SHARE_READ、SHARE_WRITE兼容，与EXCLUSIVE互斥 |
| alter table ...                               | EXCLUSIVE                            | 与其他的MDL锁互斥                              |

**意向锁**
为了避免DML在执行时，加的行锁和表锁出现冲突，在InnoDB中引入了意向锁，使得表锁不用检查每一行的数据是否加锁，使用意向锁来减少表锁的检查，从而提高效率。
意向锁的分类：
	1.意向共享锁（IS）：由语句 `select ... lock in share mode;` 添加，与表锁共享锁（read）兼容，与表锁排它锁（write）互斥。
	2.意向排他锁（IX）：由语句 `insert、update、delete、select ... for update` 添加，与表锁共享锁（read）和排它锁（write）互斥，意向锁之间不回互斥。

_查看意向锁的加锁情况_

```sql
select object_schema, object_name, index_name, lock_type, lock_data, lock_mode, lock_status from performance_schema.data_locks;
```
**行级锁**
每次操作都会锁住对应的行数据，锁的粒度最小，发生锁冲突的概率较低，并发度最高，应用在innoDB引擎中。
行锁的分类：
	InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项来加锁实现的，并不是对记录加锁。对于行级锁，主要分为以下三类：
	1.行锁（Record Lock）：锁订单个行的记录到的锁，防止其他事物对该行数据进行update、delete等操作，在RC、RR隔离模式下都支持。
	2.间隙锁（Gap Lock）：锁定索引记录间隙，确保索引记录间隙不变，防止其他事物在这个间隙insert，从而引起幻读，在RR模式下都支持。
	3.临键锁（Next-key Lock）：行锁和间隙锁的组合，锁住数据的同时锁住数据前面的间隙（Gap），在RR模式下都支持。

InnoDB实现了两种类型的行锁：
	1.共享锁（S）：允许一个事物去读取一行，阻止其他事物获取相同数据集的排它锁。（当前事物读取数据时其他事物可以读取该行数据但是不能修改）
	2.排它锁（X）：允许获取排它锁的事物更新数据，阻止其他事物获取相同数据集的共享锁和排它锁。（当前事物修改数据时，其他事物不能读取、不能修改当前行数据）

| 锁类型          | S（共享锁） | X（排它锁） |
| --------------- | ----------- | ----------- |
| **S（共享锁）** | 兼容        | 冲突        |
| **X（排它锁）** | 冲突        | 冲突        |

常见的sql语句对行数据的加锁情况

| SQL                           | 行锁类型   | 说明                                |
| ----------------------------- | ---------- | ----------------------------------- |
| INSERT ...                    | 排它锁     | 自动加锁                            |
| UPDATE ...                    | 排它锁     | 自动加锁                            |
| DELETE ...                    | 排它锁     | 自动加锁                            |
| SELECT (正常查询)             | 不加任何锁 |                                     |
| SELECT ... LOCK IN SHARE MODE | 共享锁     | 手动在sql后面添加LOCK IN SHARE MODE |
| SELECT ... FOR UPDATE         | 排它锁     | 手动在sql后面添加FOR UPDATE         |

> <font color="red">InnoDB的行锁是针对索引添加的锁，不通过索引检索数据时，InnoDB将对表中的所有记录加锁，此时**行锁升级为表锁**</font>

现在有一张用户表，表结构如下：

```sql
create table t_user_base_info
(
    id            varchar(32)      not null comment 'id' primary key,
    login_account varchar(50)      not null comment '登陆账号',
    password      varchar(50)      not null comment '登陆密码',
    username      varchar(255)     not null comment '用户名',
    user_type     int(2) default 1 not null comment '用户类型'
);
```

分别执行SQL语句

```sql
UPDATE t_user_base_info set username = '张三' where id = '001';
加行锁，因为是根据主键更新。

UPDATE t_user_base_info set username = '李四' where login_account = 'admin';
行锁升级为表锁，因为login_account字段上面没有索引。
```

