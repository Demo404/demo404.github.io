---
title: MySQL学习 ｜ 十一、数据库中的索引
date: 2023-08-02 17:57:16
tags: [数据库, MySQL]
---

#### **一、索引概述**

什么是索引‍‍?

索引（index）是帮助MySQL高效获取数据的有序数据结构。在数据之外，数据库还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构之上实现高级查找算法，这种数据结构就是索引。‍‍‍‍‍‍‍‍

索引的优点‍‍

    1、提高了检索数据的效率。

    2、通过索引列对数据进行排序，降低数据的排序成本。

索引的缺点：

    1、索引也要占用空间。

    2、索引提高了查询效率的同时也降低了更新数据（INSERT、UPDATE...）的效率。‍‍‍‍‍‍

</br>

#### **二、索引的语法**

创建索引

```
CREATE [UNIQUE | FULLTEXT | ] INDEX index_name ON table_name(index_col_name, ...);
```

查看索引

```
SHOW INDEX FROM table_name;
```

删除索引

```
DROP INDEX index_name ON table_name;
```
</br>

#### **三、索引结构**

MySQL的索引是在引擎层实现的，不同的存储引擎有不同的结构，主要包括下面几种：‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍

| 索引结构               | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| B+Tree索引             | 最常见的索引类型，大部分引擎都支持B+树索引。                 |
| Hash索引               | 底层使用hash表实现的索引，不支持范围查询，只有精确匹配的列才有效。 |
| R-tree索引（空间索引） | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少。 |
| Full-text（全文索引）  | 是一种通过建立倒排索引，快速匹配文档的方式。类似于Lucene，Solr，ES。 |

不同的索引在各个存储引擎中的支持情况：

| 索引                  | InnoDB          | MyISAM | Memory |
| --------------------- | --------------- | ------ | ------ |
| B+Tree索引            | 支持            | 支持   | 支持   |
| Hash索引              | 不支持          | 不支持 | 支持   |
| R-tree索引            | 不支持          | 支持   | 不支持 |
| Full-text（全文索引） | 5.6版本之后支持 | 支持   | 不支持 |

**数据结构在线模拟：**https://www.cs.usfca.edu/~galles/visualization/Algorithms.html

**1、Btree和Btree索引**

二叉树（大于祖先节点在右侧，小于祖先节点在左侧）

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.12.33.png?e=1690971241&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:zvz1Ze3lfMuSvZPYl5CHAl1Q6jU=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.12.33.png?e=1690971241&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:zvz1Ze3lfMuSvZPYl5CHAl1Q6jU=)

二叉树的缺点‍‍‍

(1)、顺序插入时会形成一个链表，查询性能大大降低。
(2)、数据量较大的情况下层级较深，检索速度慢。‍‍‍‍‍‍‍‍‍‍‍

红黑树（自平衡的二叉树）‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.14.20.png?e=1690976981&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:ZXZQnd1eaXR6kJSX20MKnQ_Zeqw=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.14.20.png?e=1690976981&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:ZXZQnd1eaXR6kJSX20MKnQ_Zeqw=)

红黑树的缺点：数据量较大的情况下层级较深，检索速度慢。

B树（多路平衡二叉树）

以一颗最大度数（max-degree）为5（5阶）的b-tree为例（每个节点最多可存储4个可以，5个指针）。‍‍

树的度数指得是一个节点的子节点的个数。

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.16.06.png?e=1690971299&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:0YD1De4f43jUT2NTDMKXBNlL_mM=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.16.06.png?e=1690971299&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:0YD1De4f43jUT2NTDMKXBNlL_mM=)

**2、B+树和B+树索引‍‍‍‍**

以一棵最大度数为4的b+为例‍‍‍‍‍

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.41.44.png?e=1690971335&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:ZikwIAYybTb3x5A6S59En-UllD0=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.41.44.png?e=1690971335&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:ZikwIAYybTb3x5A6S59En-UllD0=)

（1）、所有的数据都会在叶子结点出现

（2）、叶子结点会形成一个单向列表

MySQL中的B+树

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.47.32.png?e=1690971354&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:7eUqy6D6GrL7btSmKaoRg8MK7is=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-07-28%2008.47.32.png?e=1690971354&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:7eUqy6D6GrL7btSmKaoRg8MK7is=)

MySQL数据结构对经典的B+树进行了优化。在原有B+树基础之上增加了一个只下过相邻叶子结点的链表指针，就形成了带有顺序指针的B+树，提高了区间访问的性能。  

**3、hash索引**

采用一定的hash算法，将键值转换成新的hash值，映射到对应到槽位上，然后存储到hash表中。‍‍‍‍‍‍‍‍

如果两个（或多个）键值映射到同一个槽位上，他们就产生了hash冲突（也成hash碰撞），hash冲突可以通过链表来解决。‍

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-08-01%2008.20.26.png?e=1690971377&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:v1z-LvCySNFPCDhlFZ5HQMOJwME=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-08-01%2008.20.26.png?e=1690971377&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:v1z-LvCySNFPCDhlFZ5HQMOJwME=)‍

Hash索引的特点：

（1）、hash索引只能用于对等比较（=，in），不支持范围查询（between，<， >）。‍‍

（2）、无法利用索引完成排序。‍

（3）、查询效率高，通常只需要一次检索即可（hash冲突时会去查询链表），效率高于B+tree索引。‍‍‍‍‍

**思考：为什么InnoDB索引选择B+树而非二叉树或者红黑树或者B树？**‍‍‍‍‍‍

如果是顺序插入的话，二叉树会形成链表，导致树的层级较深，从而降低查找的效率，红黑树本质也是二叉树，也会有该问题。相对于二叉树，B+树层级更少，效率更高。‍‍‍‍‍‍‍

B树非叶子结点也会存储数据，导致磁盘页上存放的数据变少，相同数据量的情况下，树的高度会增加，导致效率降低。B+树的叶子节点为双向链表，便于范围查询和排序。‍‍‍‍‍

Hash索引不支持范围查询和排序。‍‍‍‍‍

  </br>

#### **四、索引分类**

| 分类     | 含义                                                   | 特点                     | 关键字   |
| -------- | ------------------------------------------------------ | ------------------------ | -------- |
| 主键索引 | 针对于表中主键创建的索引                               | 默认自动创建，只有一个。 | PRIMARY  |
| 唯一索引 | 避免同一表中某数据列的值重复                           | 可以有多个               | UNIQUE   |
| 常规索引 | 快速定位特定数据                                       | 可以有多个               |          |
| 全文索引 | 全文索引查找的是文本中的关键词，而不是比较索引中的值。 | 可以有多个               | FULLTEXT |

在InnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种：

| 分类                        | 含义                                                         | 特点                   |
| --------------------------- | ------------------------------------------------------------ | ---------------------- |
| 聚簇索引（Clustered Index） | 将数据存储与索引放在了一起，索引结构的叶子结点保存了行数据。 | 必须有，而且只有一个。 |
| 二级索引（Secondary Index） | 将数据与索引分开存储，索引结构的叶子结点关联的是对应的主键。 | 可以存在多个。         |

聚集索引的选取规则：‍‍‍‍‍‍‍‍

    1、如果存在主键，主键就是聚集索引。

    2、如果不存在主键，将使用第一个唯一索引作为聚集索引。

    3、如果表没有主键或者没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引。‍‍

聚集索引和二级索引的图示‍‍

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-08-01%2008.45.32.png?e=1690977024&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:Vgte0vIsqR5YDbA3D5hXTNPdkgI=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-08-01%2008.45.32.png?e=1690977024&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:Vgte0vIsqR5YDbA3D5hXTNPdkgI=)

聚集索引叶子节点存储的是每一行的数据（row），二级索引每一个叶子结点存储的是对应行的id。  

聚集索引和二级索引在查询中的执行过程：‍‍‍

![http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-08-01%2008.48.12.png?e=1690977040&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:nuUA0rRnOG0v51-FSVc_2drmc8M=](http://rygkvvhh1.hd-bkt.clouddn.com/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81%E4%B8%80/%E6%88%AA%E5%B1%8F2023-08-01%2008.48.12.png?e=1690977040&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:nuUA0rRnOG0v51-FSVc_2drmc8M=)

1、首先执行where条件后的查询，使用二级索引根据name拿到id。

2、根据id到聚集索引查询行数据。（回表查询）