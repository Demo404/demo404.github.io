---
title: MySQL学习一
date: 2023-07-26 18:05:02
tags:
---
**一、数据库相关的概念**  
 
数据库（DB）：存储数据的仓库，数据是有组织的进行存储。
 
数据库管理系统（DBMS）：操作和管理数据库的大型软件。‍‍‍‍‍‍‍
 
SQL：操作关系型数据库的编程语言，定义了一套操作关系型数据库的统一标准。‍‍‍‍
 
  
 
**二、主流的数据库管理系统**
 
1、Oracle：oracle公司研发，大型且收费。
 
2、MySQL：小型，有免费游收费。‍
 
3、PostgreSQL：中小型数据库，免费。
 
4、SQLite：嵌入性数据库，安卓系统采用SQLite。
 
. . . . . .‍‍‍
 
  
 
**三、MySQL的安装和启动**‍‍‍
 
版本：
 
*   社区版（MySQL Community Server）
    
    免费，MySQL不提供任何技术支持。
    
 
‍‍‍
 
*   商业版（MySQL Enterprise Edition）‍‍
    
    收费，可试用，MySQL提供技术支持。
    
 
  
 
下载地址：
 
 https://dev.mysql.com/downloads/ 或者 https://www.mysql.com/ 依次点击如下进入下载列表页  
 
下载 MySQL Community Server 版（根据自身情况选择），然后选择对应的小版本下载，最后一步可以不用登录和注册。
 
‍‍
 
安装步骤：
 
1、上传下载的安装包到服务器的 /usr/local 目录下‍
 
2、使用 tar 命令解压下载的安装包‍
 
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
 
```
`tar -xvf mysql-8.0.33-linux-glibc2.12-x86_64.tar.xz``参数说明：``-x：解压缩压缩档案的参数``-v：压缩的过程中显示档案``-f：置顶文档名，在f后面立即接文件名，不能再加参数`
```
 
3、重命名解压后的文件夹
 
*     
    
 
```
mv mysql-8.0.33-linux-glibc2.12-x86_64 mysql
```
 
4、修改MySQL的配置文件
 
*     
    
 
```
vim /etc/my.cnf
```
 
‍‍
 
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
*     
    
 
```
`[mysqld]``basedir=/usr/local/mysql``datadir=/usr/local/mysql/data``socket=/tmp/mysql.sock``character-set-server=utf8mb4``sql_mode =STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION``# Disabling symbolic-links is recommended to prevent assorted security risks``symbolic-links=0``# Settings user and group are ignored when systemd is used.``# If you need to run mysqld under a different user or group,``# customize your systemd unit file for mariadb according to the``# instructions in http://fedoraproject.org/wiki/Systemd``[mysqld_safe]``log-error=/var/log/mariadb/mariadb.log``pid-file=/var/run/mariadb/mariadb.pid``#``# include all files from the config directory``#``!includedir /etc/my.cnf.d`
```
 
‍‍
 
然后 :wq 退出编辑。‍‍‍‍‍‍‍
 
5、新建用户和用户组‍
 
*     
    
*     
    
*     
    
*     
    
 
```
cd /usr/local/mysql/groupadd mysql               # 创建用户组useradd -r -g mysql mysql    # 创建用户并且指定用户组chown -R mysql:mysql ./      # 修改当前文件的归属用户和用户组
```
 
  
 
6、初始化数据库，分别执行如下命令
 
*     
    
*     
    
*     
    
 
```
cd /usr/local/mysql/mkdir ./data./bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/ --initialize
```
 
  
 
第三步命令如果报错：‍‍‍‍./bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory 的话说明当前缺少了libaio.so.1库，执行如下命令安装libaio.so.1库。
 
*     
    
 
```
 yum install -y libaio
```
 
安装成功后再次执行第三条命令，出现如下界面则说明安装成功了，最后的初始化密码要记住。
 
7、添加MySQL到系统服务中并且建立软连接‍
 
*     
    
*     
    
*     
    
*     
    
 
```
cp -a ./support-files/mysql.server /etc/init.d/mysqlchmod +x /etc/init.d/mysqlchkconfig --add mysqlchkconfig --list mysql #检查是否生效
```
 
建立软连接
 
*     
    
 
```
ln -s /usr/local/mysql/bin/mysql /usr/bin
```
 
8、启动并且登录MySQL
 
执行 service mysql start命令，出现Success则说明启动成功。 
 
登录MySQL‍
 
```
mysql -u root -p
```
 
回车然后输入刚才的初始化密码，输入时光标并不会有任何变化。  
 
修改用户密码
 
*     
    
*     
    
 
```
ALTER USER "root"@"localhost" IDENTIFIED  BY "你的新密码";FLUSH PRIVILEGES;  #配置生效
```
 
修改如下配置，使得root用户可以远程连接。‍‍
 
*     
    
*     
    
*     
    
 
```
use mysql;  update user set host='%' where user ='root';FLUSH PRIVILEGES;    #配置生效
```
 
  
 
四、MySQL的数据模型
 
关系型数据库（RDBMS）
 
概念：建立在关系型模型基础上，由多张相互连接的二维表组成的数据库。
 
特点：1、使用表结构存储。格式统一便于维护。2、使用SQL语言操作，标准统一使用方便。