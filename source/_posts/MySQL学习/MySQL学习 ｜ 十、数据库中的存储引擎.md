---
title: MySQL学习 ｜ 十、数据库中的存储引擎
date: 2023-08-02 17:36:37
tags: [数据库, MySQL]
---
#### **一、MySQL的体系结构**‍

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E.png?e=1690969816&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:9KDlAtWfcLiGGCetaUnuygJLMyM=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E.png?e=1690969816&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:9KDlAtWfcLiGGCetaUnuygJLMyM=)

**连接层：**最上层是一些客户端和链接服务，主要完成一些连接处理、授权认证以及相关的安全方案。服务器也会为安全接入的每个客户端验证它所具备的操作权限。‍

**服务层：**主要完成大多数的核心功能，如SQL接口，并完成查询的缓存，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能在这一层实现，如存储过程和视图等。‍‍‍‍‍‍‍‍‍

**引擎层：**存储引擎真正的负责了MYSQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样就可以根据自己的需要来选择合适的存储引擎。‍

**存储层：**主要是将数据存储在文件系统之上并完成与存储引擎的交互。‍‍‍‍

  </br>

#### **二、存储引擎的简介**

1、什么是存储引擎‍‍‍

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是基于表而非基于库的，所以存储引擎也可被称为表类型。‍‍‍‍

2、查看数据库支持的存储引擎‍‍

```
SHOW ENGINES;
```

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E6%95%B0%E6%8D%AE%E5%BA%93%E6%94%AF%E6%8C%81%E7%9A%84%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E.png?e=1690969853&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:mEEDDkFMIKlNvxqUtxr-wdvm98g=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E6%95%B0%E6%8D%AE%E5%BA%93%E6%94%AF%E6%8C%81%E7%9A%84%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E.png?e=1690969853&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:mEEDDkFMIKlNvxqUtxr-wdvm98g=)

3、查看已创建表的存储引擎‍

```
SHOW CREATE TABLE tb_wxuser;
```

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E8%A1%A8%E4%BD%BF%E7%94%A8%E7%9A%84%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E.png?e=1690969896&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:KNftZ9mpUuHLN6Bp4gbQaygE_SY=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E8%A1%A8%E4%BD%BF%E7%94%A8%E7%9A%84%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E.png?e=1690969896&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:KNftZ9mpUuHLN6Bp4gbQaygE_SY=)

</br>

#### **三、常见的存储引擎**

**1、InnoDB存储引擎：**InnoDB是一种兼顾可靠性和高性能的通用存储引擎，在MySQL5.5之后InnoDB是默认的存储引擎。

InnoDB的特点：

（1）、DML遵循ACID模型，<font color=navy>支持事物</font>。‍

（2）、<font color=navy>支持行及锁</font>，提高并发访问性能。‍‍‍‍

（3）、<font color=navy>支持外键FOREIGN KEY约束</font>，保证数据的完整性和正确性。‍‍‍

InnoDB的文件存储：

XXX.ibd:XXX代表的是数据库的表名，InnoDB引擎的每张表都会对应这样一个表空间文件，存储该表的表结构（frm，sdi）、数据和索引。‍‍‍

参数：innodb_file_per_table = ON 表示每一个表对应一个ibd存储文件，OFF表示共用一个ibd存储文件。‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍

InnoDB的存储结构：

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/InnoDB%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84.png?e=1690969936&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:Y2AZUI0hEAE_srJXL-yr9olEJm0=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/InnoDB%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84.png?e=1690969936&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:Y2AZUI0hEAE_srJXL-yr9olEJm0=)

**2、MyISAM存储引擎：**MyISAM是MySQL早期的默认存储引擎。

MyISAM引擎特点：  

（1）：不支持事物、不支持外键。

（2）：支持表锁但不支持行锁。

（3）：<font color=navy>访问速度较快</font>。  

MyISAM引擎的文件存储：

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/MyISAM%E6%96%87%E4%BB%B6%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84.png?e=1690970005&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:yfCm14dlhf13ed--rIzByxZpC_E=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/MyISAM%E6%96%87%E4%BB%B6%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84.png?e=1690970005&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:yfCm14dlhf13ed--rIzByxZpC_E=)

**3、Memory存储引擎：**Memory引擎的表数据存储在内存中，容易受到硬件问题、断电等的影响，因此只能将这些表作为临时表或者缓存使用。

Memory引擎特点：

（1）、<font color=navy>内存存放数据</font> 。

（2）、默认hash索引。

Memory引擎文件存储：

xxx.sdi: 存储表结构信息

**4、innoDB、MyISAM、Memory三者的区别**

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E4%B8%89%E7%A7%8D%E5%BC%95%E6%93%8E%E7%9A%84%E5%8C%BA%E5%88%AB.png?e=1690970020&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:nztTC5YX8Y7lmiUlMH9YCTGh1YM=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E5%8D%81/%E4%B8%89%E7%A7%8D%E5%BC%95%E6%93%8E%E7%9A%84%E5%8C%BA%E5%88%AB.png?e=1690970020&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:nztTC5YX8Y7lmiUlMH9YCTGh1YM=)