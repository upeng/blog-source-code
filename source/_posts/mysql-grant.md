---
title: mysql添加用户管理员及授权
date: 2016-04-10 14:26
tags: mysql
categories: MySQL
---

## mysql添加用户管理员及授权

比如添加test用户，grant 高级 DBA 管理 MySQL 中所有数据库的权限

### step1 创建用户
```
insert into mysql.user(Host,User,Password) values("%","test",password("123qwe"));
insert into mysql.user(Host,User,Password) values("localhost","test",password("123qwe"));
注：%和localhost的区别
此处的"localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录。如果想远程登录的话，将"localhost"改为"%"，表示在任何一台电脑上都可以登录。也可以指定某台机器可以远程登录。
```
### step2 为用户授权
```
授权格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码";　
grant all privileges on testDB.testTable to test@localhost identified by '1234';
grant all on *.* to test@'%';  //全部数据库表的权限
```
### step3 刷新系统权限表
```
flush privileges; //刷新系统权限表
```