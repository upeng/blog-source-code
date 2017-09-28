---
title: mysql主从复制原理理解与实践
date: 2016-12-05 22:40:03
tags:
  - mysql
categories:
  - MySQL
---


只从最简单的mysql主从复制原理与实践着手，之前看到Yshiba老师讲解的挺细致透彻，顺便做下笔记并实践一下。

## [](#主从复制原理-replication "主从复制原理 replication")主从复制原理 replication

主服务器(master)的binlog,记录master的各种操作（insert、update、delete），master的磁盘只要发生变化就会记录到自己的binlog里面。

从服务器（slave）如果能读取到master的binlog,那么就可以将master的所做操作跟着执行一遍。但是由于master的binlog中含有master的一些信息，所以需要对binlog进行分析加工，slave的relaylog就是干这种事的，实际上slave将使用relaylog终极日志上的sql操作，保证主从数据一致性。
<a id="more"></a>
![mysql-master-slave](https://raw.githubusercontent.com/upeng/upeng.github.io/master/image/mysql-master-slave.gif)
<!-- more -->
**总结**：

1.  master要配置binlog
2.  slave要配置relaylog
3.  master需要grant slave账号使得slave有权读取master的binlog
4.  slave使用账号连接master

## [](#主从配置过程 "主从配置过程")主从配置过程

### [](#编辑master的配置文件my-cnf "编辑master的配置文件my.cnf")编辑master的配置文件my.cnf

```
#1.配置server-id
server-id=101

#2.开启二进制日志
log-bin=mysql-bin

#3.指定二进制日志格式
binlog-format=mixed

```

> 二进制日志格式binlog-format

1.  statement：二进制记录的是执行语句，update insert…
2.  row：二进制执行的是磁盘变化
3.  mixed：混合的，由系统根据语句决定

> 到底使用哪种format，根据实际情况判断

**比如：**
磁盘变化少，语句长，使用row
`update user set age = age + 1 where id = 2;`
磁盘变化多，语句短，使用statement
`update slary = slary + 1000;` (10w条数据)

### [](#编辑slave的配置文件my-cnf "编辑slave的配置文件my.cnf")编辑slave的配置文件my.cnf

```
#1.配置server-id
server-id=102

#2.开启二进制日志(备份)
log-bin=mysql-bin

#3.创建relay日志
relay-log=mysql-relay

```

> 配置完成后重启两个机器的mysql服务器
> `service mysqld restart`

## [](#启动主从建立关系 "启动主从建立关系")启动主从建立关系

### [](#master的操作 "master的操作")master的操作

查看主服务器的状态
`show master status`

```
#授权账号密码
grant replication clinet,replication slave on *.* to test@'192.168.1.%' identified by '123qwe';

flush privileges;

```

### [](#slave的操作 "slave的操作")slave的操作

建立连接

```
change master to
master_host='192.168.1.101'
master_user='test'
master_password='123qwe'
master_log_file='mysql-bin.000001'
master_log_pass=348

```

查看连接状态
`show slave status`

启动从服务器同步进程并查看状态
`start slave`

> 如果报错Last_IO_Error: error connect to master ‘test@192.168.1.101:3306’ ……
> 使用Telnet 192.168.1.101 3306排查连接master是否成功
> 有可能是防火墙开了，关闭防火墙：`services iptables stop`

```
stop slave;
start slave;
show slave status;

```

### [](#验证测试 "验证测试")验证测试

在mater中创建数据库和表，并做各种增删改操作，到从库观察验证

## [](#补充说明 "补充说明")补充说明

**mysql经常报错如下：**
`can't connect to local mysql server through socket '/tmp/mysqld.sock'`
通过`/tmp/mysqld.sock`下的socket文件连接不成功，比如我们默认安装的mysql通过配置文件可以发现mysql.sock在`/var/lib/mysql/mysql.sock`下。也就是说/tmp/下没有mysqld.sock文件

**有两种解决方法：**

1.  指定sock文件位置
    `mysql -uroot -p -S /var/lib/mysql/mysql.sock`
2.  建立一个软连接
    `ln -s /var/lib/mysql/mysql.sock /tmp/mysqld.sock`

## [](#后续学习计划 "后续学习计划")后续学习计划

主从复制可能会出现的问题级解决方案
mysql读写分离策略