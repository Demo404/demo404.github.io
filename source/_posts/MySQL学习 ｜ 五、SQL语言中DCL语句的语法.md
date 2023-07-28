---
title: MySQL学习 ｜ 五、SQL语言中DCL语句的语法
date: 2023-07-28 16:17:39
tags:
---

**DCL：数据控制语言，用来管理数据库用户、数据库访问权限。**

#### **一、DCL管理用户**

创建用户


```
CRAETE USER '用户名'@'主机' IDENTIFIED BY '密码';
```

主机:  localhost:当前主机；%:任意机器。‍‍‍‍‍‍

查询用户


```
SELECT * FROM USER;
```

修改用户密码  


```
ALTER USER '用户名'@'主机' IDENTIFIED WITH mysql_native_password '密码';
```

删除用户  


```
DROP USER '用户名'@'主机';
```

  </br>

#### **二、DCL权限控制**‍‍

mysql中常用的权限列表‍‍‍‍‍‍‍

| 权限                | 说明               |
| ------------------- | ------------------ |
| ALL, ALL PRIVILEGES | 所有权限           |
| SELECT              | 查询数据           |
| INSERT              | 插入数据           |
| UPDATE              | 修改数据           |
| DELETE              | 删除数据           |
| ALTER               | 修改表             |
| DROP                | 删除数据库/表/视图 |
| CREATE              | 创建数据库/表      |

查询权限


```
SHOW GRANTS FOR '用户名'@'主机';
```

授予权限  


```
GRANT 权限列表 ON 数据库.表名 TO '用户名'@'主机';
```

撤销权限  


```
REVOKE 权限列表 ON 数据库.表名 FROM '用户名'@'主机';
```

‍‍‍