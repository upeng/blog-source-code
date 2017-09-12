---
title: linux下导入、导出mysql数据库命令
date: 2015-08-03 11:09
tags: mysql
categories: MySQL
---

## 一、导出数据库用mysqldump命令（注意mysql的安装路径，即此命令的路径）：
* ### 导出数据和表结构：
mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql
eg /usr/local/mysql/bin/   mysqldump -uroot -p abc > abc.sql
敲回车后会提示输入密码

* ### 只导出表结构
mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql
eg /usr/local/mysql/bin/   mysqldump -uroot -p -d abc > abc.sql

注：/usr/local/mysql/bin/  --->  mysql的data目录


## 二、导入数据库
1. ### 首先建空数据库
mysql>create database abc;

2. ### 导入数据库
方法一：
（1）选择数据库
mysql>use abc;
（2）设置数据库编码
mysql>set names utf8;
（3）导入数据（注意sql文件的路径）
mysql>source /home/abc/abc.sql;
方法二：
mysql -u用户名 -p密码 数据库名 < 数据库名.sql
eg mysql -uabc_f -p abc < abc.sql

建议使用第二种方法导入。

### 三、关于导入导出数据库需注意的地方（总是忘记！）
+ `而导入数据库和导出正好相反（废话），要先进入mysql，然后使用source命令`

+ `导出数据库是在linux命令行模式中，当前位于哪个目录就导出到哪里（途中可能需要输入user password）` 