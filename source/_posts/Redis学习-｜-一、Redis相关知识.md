---
title: Redis学习 ｜ 一、Redis相关知识
date: 2025-12-26 10:14:02
tags:
---

#### Redis相关知识和数据类型

##### 🍃 Redis一些基础

1、默认有`16`个数据库，出事默认使用`0`号数据库。
2、使用命令 `select <dbid>` 切换数据库，如 `select 8` 切换到8号数据库。
3、`dbsize` 查看当前数据库的`key`的数量。
4、`flushdb` 清空当前数据库的缓存数据。
5、`flushall` 清空所有数据库的缓存数据。
6、相对于 `memcached`，`Redis` 支持的数据类型更加丰富，内存中的数据也支持持久化。

###### Redis为单线程+多路IO复用技术
多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入了多个文件描述符，如果有一个文件描述符就绪则返回，否则阻塞直到超时。等到就绪状态后可以在一个线程中进行真正的操作，也可以启用线程池。

##### ⚙️ Redis中key键操作
- `keys *` 查看当前数据库中的key
- `exists <key>` 判断key是否存在
    ```
    127.0.0.1:6379> exists k1
    (integer) 0
    127.0.0.1:6379> exists sys_dict:sys_user_sex
    (integer) 1
    ```
- `type <key>` 判断key的类型
    ```
    127.0.0.1:6379> type sys_dict:sys_user_sex
    string
    ```
- `del <key>` 删除指定的key数据
- `unlink <key>` 根据value选择非阻塞删除（仅将key删除，数据会在后续异步删除）
- `expire <key> 10` 为指定的key设置过期时间为10秒
- `ttl <key>` 查看kay还有多久过期，-1表示不过期，-2表示已过期
- `dbsize` 查看当前数据库中key的数量
- `select <dbid>` 切换数据库

Redis中常见的数据类型有 `list`, `set`, `hash`, `zset`, `string` 五种。
