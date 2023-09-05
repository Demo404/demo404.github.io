---
title: MySQL学习 ｜ 一、MySQL的基本概念和安装
date: 2023-07-26 18:05:02
tags: [数据库, MySQL]
---
#### **一、数据库相关的概念**

数据库（DB）：存储数据的仓库，数据是有组织的进行存储。

数据库管理系统（DBMS）：操作和管理数据库的大型软件。‍‍‍‍‍‍‍

SQL：操作关系型数据库的编程语言，定义了一套操作关系型数据库的统一标准。  

​		  

#### **二、主流的数据库管理系统**

1、Oracle：oracle公司研发，大型且收费。

2、MySQL：小型，有免费有收费。‍

3、PostgreSQL：中小型数据库，免费。

4、SQLite：嵌入性数据库，安卓系统采用SQLite。

. . . . . .‍‍‍

​		

#### **三、MySQL的安装和启动**‍‍‍

##### 版本：

*   社区版（MySQL Community Server）
    
    免费，MySQL不提供任何技术支持。


*   商业版（MySQL Enterprise Edition）‍‍
    
    收费，可试用，MySQL提供技术支持。  

###### 下载地址：
https://dev.mysql.com/downloads/ 或者 https://www.mysql.com/ 依次如下点击进行下载。

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E4%B8%8B%E8%BD%BD%E6%8C%89%E9%92%AE.jpeg?e=1690468324&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:iAo6vdQ_978yosZckapZZMKMy6w=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E4%B8%8B%E8%BD%BD%E6%8C%89%E9%92%AE.jpeg?e=1690468324&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:iAo6vdQ_978yosZckapZZMKMy6w=)

下拉页面找到download

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E4%B8%8B%E8%BD%BD%E6%8C%89%E9%92%AE.jpeg?e=1690468265&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:kXAMjXltrPzX0yTS0c97bes7y6c=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E4%B8%8B%E6%BB%91%E5%88%B0%E8%BF%99.jpg?e=1690468193&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:fp3gxEeu7DPFbCob1ObFXe_w5Wc=)

下载 MySQL Community Server 版（根据自身情况选择）

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E9%80%89%E6%8B%A9MySQL%E7%B1%BB%E5%9E%8B.jpg?e=1690468468&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:H-gIHLLX6QQEv5yeGduvN3aFC6g=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E9%80%89%E6%8B%A9MySQL%E7%B1%BB%E5%9E%8B.jpg?e=1690468468&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:H-gIHLLX6QQEv5yeGduvN3aFC6g=)

然后选择对应的版本下载

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E9%80%89%E6%8B%A9%E7%89%88%E6%9C%AC.jpg?e=1690468489&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:KpcEtMAi5EEjDKoP9fvVAzClt8E=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E9%80%89%E6%8B%A9%E7%89%88%E6%9C%AC.jpg?e=1690468489&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:KpcEtMAi5EEjDKoP9fvVAzClt8E=)

最后一步可以不用登录和注册。

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E4%B8%8D%E7%94%A8%E7%99%BB%E5%BD%95%E4%B8%8B%E8%BD%BD.jpg?e=1690468523&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:HPF2Xg3KkIkWZWhCeyN6fYPXNLI=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E4%B8%8D%E7%94%A8%E7%99%BB%E5%BD%95%E4%B8%8B%E8%BD%BD.jpg?e=1690468523&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:HPF2Xg3KkIkWZWhCeyN6fYPXNLI=)

###### 安装步骤：
1、上传下载的安装包到服务器的 /usr/local 目录下‍

2、使用 tar 命令解压下载的安装包‍

 ```shell
tar -xvf mysql-8.0.33-linux-glibc2.12-x86_64.tar.xz
参数说明：
-x：解压缩压缩档案的参数
-v：压缩的过程中显示档案
-f：置顶文档名，在f后面立即接文件名，不能再加参数
 ```

3、重命名解压后的文件夹
```shell
mv mysql-8.0.33-linux-glibc2.12-x86_64 mysql
```

4、修改MySQL的配置文件如下

```shell
vim /etc/my.cnf
```
```shell
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

[mysqld]
skip-name-resolve
character_set_server=utf8
init_connect='SET NAMES utf8'
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

#最大连接数
max_connections=1000
```
 然后 `:wq` 退出编辑。‍‍‍‍‍‍‍

5、新建用户和用户组‍

```shell
cd /usr/local/mysql/
groupadd mysql               # 创建用户组
useradd -r -g mysql mysql    # 创建用户并且指定用户组
chown -R mysql:mysql ./      # 修改当前文件的归属用户和用户组
```

6、初始化数据库，分别执行如下命令

```shell
cd /usr/local/mysql/
mkdir ./data
./bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/ --initialize
```

第三步命令如果报错：‍‍‍‍./bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory 的话说明当前缺少了libaio.so.1库，执行如下命令安装libaio.so.1库。

```shell
yum install -y libaio
```

安装成功后再次执行第三条命令，出现如下界面则说明安装成功了，最后的初始化密码要记住。

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E5%AE%89%E8%A3%85%E6%88%90%E5%8A%9F%EF%BC%8C%E8%AE%B0%E4%BD%8F%E5%AF%86%E7%A0%81.png?e=1690468658&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:PoIVlSt2_-0a9EeMdCnSPJrKiK8=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E5%AE%89%E8%A3%85%E6%88%90%E5%8A%9F%EF%BC%8C%E8%AE%B0%E4%BD%8F%E5%AF%86%E7%A0%81.png?e=1690468658&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:PoIVlSt2_-0a9EeMdCnSPJrKiK8=)

7、添加MySQL到系统服务中并且建立软连接‍

```shell
cp -a ./support-files/mysql.server /etc/init.d/mysql
chmod +x /etc/init.d/mysql
chkconfig --add mysql
chkconfig --list mysql #检查是否生效
```


建立软连接

```shell
ln -s /usr/local/mysql/bin/mysql /usr/bin
```

8、启动并且登录MySQL

执行 `service mysql start` 命令，出现 `Success`字样则说明启动成功。 

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E5%90%AF%E5%8A%A8%E6%88%90%E5%8A%9F.png?e=1690468732&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:Ms6BaytLZKZVv0bAERBPbn25YlE=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E5%90%AF%E5%8A%A8%E6%88%90%E5%8A%9F.png?e=1690468732&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:Ms6BaytLZKZVv0bAERBPbn25YlE=)

登录MySQL‍

```shell
mysql -u root -p
```


回车然后输入刚才的初始化密码，输入时光标并不会有任何变化。  

修改用户密码

```shell
ALTER USER "root"@"localhost" IDENTIFIED  BY "你的新密码";
FLUSH PRIVILEGES;  #配置生效
```


修改如下配置，使得root用户可以远程连接。‍‍

```shell
use mysql;  
update user set host='%' where user ='root';
FLUSH PRIVILEGES;    #配置生效
```

​		

#### 四、MySQL的数据模型

##### 关系型数据库（RDBMS）

概念：建立在关系型模型基础上，由多张相互连接的二维表组成的数据库。

特点：
&emsp;&emsp; - 1、使用表结构存储。格式统一便于维护。
&emsp;&emsp; - 2、使用SQL语言操作，标准统一使用方便。

![http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B-%E4%B8%A4%E8%A1%A8%E5%85%B3%E7%B3%BB.png?e=1690468783&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:t-Vii9Y0MNOAsNm4RBNWX7R9Qm8=](http://blog.cdn.goudan.ltd/Blog/MySQL%E5%AD%A6%E4%B9%A0%E4%B8%80/%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B-%E4%B8%A4%E8%A1%A8%E5%85%B3%E7%B3%BB.png?e=1690468783&token=w2Ck1K8J7F3OPBNX_GiYGMLReW7R0l5CDG2QUNPh:t-Vii9Y0MNOAsNm4RBNWX7R9Qm8=)