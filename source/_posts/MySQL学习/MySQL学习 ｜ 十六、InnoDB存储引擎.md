---
title: MySQL学习 ｜ 十六、InnoDB存储引擎
date: 2023-09-07 07:54:09
tags: [数据库, MySQL]
---

#### 一、逻辑存储结构

![https://minio.goudan.ltd/hexo-blog/MySQL学习十六/InnoDB存储引擎逻辑结构.png](https://minio.goudan.ltd/hexo-blog/MySQL学习十六/InnoDB存储引擎逻辑结构.png)

**表空间**（idb文件），一个mysql实例可以对应多个表空间，用于存储记录、索引等数据。

**段**分为数据段、索引段、回滚段，InnoDB是索引组织表，数据段就是B+树的叶子结点，索引段为B+树的非叶子结点。段用来管理多个区（Extent）。

**区**，表空间的单元结构，每个区的大小为1M。默认情况下，InnoDB存储引擎页大小为16K，即一个区中有64个连续页。

**页**，是InnoDB存储引擎磁盘管理的最小存储单元，每一个页的大小默认为16K。为了保证数据的连续性，InnoDB存储引擎每次都会申请4～5个区。

**Row** 行，InnoDB存储引擎是按行存放数据的。

​	*Trx_id：每次对某条记录修改时，都会把事物id赋给trx_id隐藏列。*

​	*Roll point：每次对某条记录修改时，都会把旧的数据写入到undo日志中，然后这个隐藏列就相当于一个指针，可以通过它来找到修改前的信息。*

</br>

#### 二、架构

下面为InnoDB的架构图，其中左侧为内存结构，右侧为磁盘结构，中间为后台线程。

![https://minio.goudan.ltd/hexo-blog/MySQL学习十六/InnoDB的架构.png](https://minio.goudan.ltd/hexo-blog/MySQL学习十六/InnoDB的架构.png)

##### 1、内存结构

**Buffer Pool**：缓冲池为主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据（如果缓冲池中没有则从磁盘加载数据并且缓存），然后再以一定的频率刷新到磁盘，从而减少磁盘IO，加快处理速度。

缓冲池以Page为单位，底层采用链表数据结构管理Page。根据状态可以将Page分为三类：

​	1、free page：空闲page，未被使用。

​	2、clean page：被使用的page，数据未被修改。

​	3、dirty page：脏页，被使用的page，数据被修改过，页中的数据与磁盘中的数据不一致。

**Change Buffer**：更改缓冲区（针对于非唯一的二级索引），在执行DML语句时，如果这些数据Page没有在Buffer Pool中，不会直接操作磁盘，而是从数据更改存储在更改缓冲区ChangeBuffer Pool中，在未来数据被读取时，将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。

Change Buffer的意义是什么？

与聚集索引不同，二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新可能会影响索引树中不相邻的二级索引页，如果每一次操作都去操作磁盘，则会引起非常大的磁盘IO，有了Change Buffer之后，就可以在缓冲池中进行合并操作，减少磁盘IO。

**Adaptive hash index**：自适应hash索引，用于优化对Buffer Pool数据的查询。InnoDB存储引擎会监控对表上各索引页的查询，如果观察到hash索引可以提升查询速度，则建立hash索引，称之为自适应hash索引。<font color="red">自适应hash索引无需人工干预，系统根据情况会自动生成。</font>

参数：adaptive_hash_index

**Log Buffer**：日志缓冲区，用来保存要写入磁盘的log日志数据（包括redo log、undo log），默认大小为16M，日志缓冲区中的日志会定期刷新到磁盘中。如果需要更新、插入、删除许多行的事物，增大日志缓冲区的大小可以节省磁盘IO。

参数：

innodb_log_buffer_size：缓冲区大小

Innodb_flush_log_at_trx_commit：日志刷新到磁盘时机（1:日志在每次提交事物时写入磁盘文件；0:每秒将日志写入到磁盘文件一次；2:日志在每次提交事物后写入磁盘文件并且每秒刷新日志到磁盘文件）

##### 2、磁盘结构
**System Tablespace：**系统表空间时更改缓冲区的存储区域。如果表是在系统表空间而不是每个表空间文件或者通用的表空间中创建的，他可能包含表和索引文件。（MySQL5.x版本还包括InnoDB数据字典和undo等）

参数：innodb_data_file_path

**File-pre-table Tablespace：**每个表的文件表空间，包含单个InnoDB表的数据和索引，并存储在文件系统上的单个数据文件中。

参数：innodb_file_pre_table

**General Tablespace：**通用表空间，需要通过CREATE TABLESPACE语法创建表空间，在创建表时可以指定该表空间。

语法

```sql
CREATE TABLESPACE xxx ADD DEFAULT 'file_name' ENGINE = engine_name;
```

```sql
CREATE TABLE xxx ... TABLESPACE tablespace_name;
```

**Undo Tablespace：**撤销表空间，MySQl实例在初始化的时候会自动创建两个默认的undo表空间（初始大小16M），用于存储undo log。

**Temporary Tablespace：**InnoDB使用回话临时表空间和全局临时表空间，存储用户创建的临时表等数据。

**Doublewrite Buffer File：**双写缓冲区，InnoDB引擎将数据也从Buffer Pool刷新到磁盘前，先将数据页写入到双写缓冲区文件中，便于系统异常时恢复数据。

相关文件：#ib_xxxxx_0.dblwr、#ib_xxxxx_1.dblwr

**Redo Log：**重做日志，用来实现事物的持久性。该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log），前者是在内存中，后者在磁盘中。当事物提交之后会把所有的修改信息都存储在该日志中，用于在刷新脏页到磁盘时发生错误后恢复数据使用。

以循环方式写入重做日志文件，涉及两个文件：ib_logfile0、ib_logfile_1

##### 3、后台线程

1. **Master Thread：**核心后台进程，负责调度其他线程，还负责异步将缓冲池中的数据刷新到磁盘中，保持数据的一致性，还包括脏页的刷新、合并插入缓存、undo页的回收。

2. **IO Thread：**在InnoDB存储引擎中大量使用了AIO来处理IO请求，这样可以极大提高数据库的IO性能，而IO线程主页负责这些IO请求的回调。

  执行命令`show engine innodb status;`可查看到相应的信息。

  ![https://minio.goudan.ltd/hexo-blog/MySQL学习十六/IO个数查看.png](https://minio.goudan.ltd/hexo-blog/MySQL学习十六/IO个数查看.png)

  | 线程类型             | 默认个数 | 职责                         |
  | -------------------- | -------- | ---------------------------- |
  | Read Thread          | 4        | 负责读操作                   |
  | Write Thread         | 4        | 负责写操作                   |
  | Log Thread           | 1        | 负责将日志缓冲区刷新到磁盘   |
  | insert buffer thread | 1        | 负责将写缓冲区内容刷新到磁盘 |

3. **Purge Thread：**主要用于回收事物已经提交的undo log，在事物提交后，undo log可能就不用了，就用它来回收。

4. **Page Cleaner Thread：**协助Master Thread刷新脏页到磁盘的线程，他可以减轻Master Thread的压力，减少阻塞。

   </br>

#### 三、事物原理

***事物：**是一组操作的集合，它是一个不可分割的工作单元，事物会把所有的操作视为一个整体一起向系统提交或者撤销操作请求，即这些操作要么全部成功，要么全部失败。*

**特性：**

- 原子性（Atomicity）：事物是不可分割的最小单元，要么全部成功，要么全部失败。
- 一致性（Consistency）：事物完成时，必须使所有的数据保持一致的状态。
- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事物在不受外部并发操作影响的独立环境下运行。
- 持久性（Durability）：事物一旦提交或回滚，他对数据库中的数据的改变是永久的。

**redo log：**重做日志，记录的是事物提交时数据页的物理修改，用来<font color="red">实现事物的持久性</font>。该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log file），前者是在内存中，后者在磁盘上。当事物提交时会把所有的修改信息存储在该日志文件中，用于刷新脏页数据到磁盘时发生错误后进行数据恢复使用。

![https://minio.goudan.ltd/hexo-blog/MySQL学习十六/Redo日志原理.png](https://minio.goudan.ltd/hexo-blog/MySQL学习十六/Redo日志原理.png)

客户端请求过来之后，首先去BufferPool中查找是否存在要操作的数据，如果不存在则从data文件中加载到BufferPool中然后操作，同时将操作记录到RedoLog Buffer中，RedoLog Buffer中的再写入到redo log file中，后台线程在刷新脏页数据到磁盘文件时如果出错可以借助redo log file进行恢复。

为什么redo log buffer会直接写入磁盘而Buffer Pool时后台线程刷新到磁盘？因为redo log buffer写入到磁盘是顺序磁盘写入，效率要远高于Buffer Pool写入磁盘的随机磁盘写入。

WAL(Write Ahead Log)预写日志，是数据库系统中常见的一种手段，用于保证数据操作的原子性和持久性。

**undo log：**回滚日志，用于记录数据被修改前的信息，作用包含两个：提供回滚和MVCC（多版本并发控制）。

undo log和redo log记录物理日志不一样，记录的是逻辑日志。可以认为当客户端执行一条delete语句时，它会记录一条相应的insert语句，当执行一条update语句时，它会记录一条反向的update语句。当rollback时，就可以从undo log中读取相应的记录进行回滚操作。

undo log销毁：undo log在事物执行时产生，在事物提交时并不会立即删除，因为这些日志还可能用于MVCC。

undo log存储：undo log采用段的方式管理和记录，存储在rollback segement回滚段中，内部包含1024个undo log segement。



> <font color="red">MySQL中如何保证事物的四大特性？</font>
>
> ​			RedoLog保证了事物的持久性
>
> ​			UndoLog保证了事物的原子性
>
> ​			RedoLog+UndoLog保证了事物的一致性
>
> ​			MVCC+锁保证了事物的隔离型

</br>



#### 四、MVCC

##### 1、mvcc中的一些基本概念

**当前读：**读取的是记录的最新版本，读取时还要保证其他并发事物不能修改当前记录，会对读取的记录进行加锁。对于我们的日常操作，如select ... lock in share mode（共享锁）, select ... for update、update、insert、delete（排它锁）都是一种当前读。

> 同时开启两个事物A和B，事物A对表中的id=1的记录进行修改后并未提交，事物B查询时为修改前的数据，事物A提交事物后事物B查询时还是修改前的数据，此时将事物B的查询语句后增加lock in share mode可读取到id=1的最新数据。

**快照读：**简单的select查询就是快照读，读取的是记录数据的可见版本，有可能是历史数据，不会加锁，是非阻塞读。

- Read Committed：每次select都生成一个快照读。
- Repeatable Read：开启事物后第一个select语句才是快照读的地方。
- Serializable：快照读会退化为当前读。

**MVCC：**多版本并发控制。指的是维护数据库的多个版本，使得读写操作没有冲突，快照读为MySQL实现，MVCC提供了一个非阻塞读功能，MVCC的具体实现还的依赖于数据库中的三个隐式字段、undo log、readView。

##### 2、隐藏字段

![https://minio.goudan.ltd/hexo-blog/MySQL学习十六/MySQL中的隐藏字段示例.png](https://minio.goudan.ltd/hexo-blog/MySQL学习十六/MySQL中的隐藏字段示例.png)

创建用户表user时包含了三个字段，但实际在数据库中是6（或者5个）个字段。

| 隐藏字段    | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| DB_TRX_ID   | 最近修改事物的ID，记录插入这条数据或者最后一次修改这条数据的事物ID |
| DB_ROLL_PTR | 回滚指针，记录这条记录的上一个版本，用于配合undo log，指向上一个版本。 |
| DB_ROW_ID   | 隐藏主键，如果表结构没有指定主键，将会生成该隐藏主键         |

> 在表空间文件中，使用命令 `idb2sdi xxx.idb` 命令可查看到完整的表信息，其中就包含DB_TRX_ID、DB_ROLL_PTR、DB_ROW_ID这三个字段。

##### 3、undo log

![https://minio.goudan.ltd/hexo-blog/MySQL学习十六/undolog版本链.png](https://minio.goudan.ltd/hexo-blog/MySQL学习十六/undolog版本链.png)

不同事物或相同事物对同一记录进行修改，会导致该纪录的undo log会生成一条记录版本链表，链表的头部是最新的旧记录，链表的尾部是最早的旧记录。

##### 4、readView

ReadVIew（读视图）是快照读SQL执行时MVCC读取数据的依据，记录并且维护系统当前活跃的事物（未提交）ID。

ReadVIew中包含了四个核心字段

| 字段           | 含义                                                 |
| -------------- | ---------------------------------------------------- |
| m_ids          | 当前活跃的事物ID集合                                 |
| min_trx_id     | 最小活跃事物ID                                       |
| max_trx_id     | 预分配事物ID，当前最大事物ID+1（因为事物ID是自增的） |
| creator_trx_id | ReadVIew创建者的事物ID                               |

![https://minio.goudan.ltd/hexo-blog/MySQL学习十六/数据链版本.png](https://minio.goudan.ltd/hexo-blog/MySQL学习十六/数据链版本.png)
