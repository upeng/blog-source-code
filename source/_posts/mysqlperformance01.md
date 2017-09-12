---
title: mysql之sql优化-慢查询日志开启和查看（1）
date: 2016-04-24 03:25:05
tags: mysql
categories: MySQL
---

如何发现有问题的SQL？使用慢查询日志【slow_query_log】对有效率问题的sql进行监控
### 查看慢查询日志开启状态
```
show variable  s like 'slow_query_log'
```

![](https://raw.githubusercontent.com/upeng/upeng.github.io/master/image/6a0acb38-fbe0-42e6-b9ca-f05955ca73e8.png)
### 记录未使用索引的查询
未使用索引的查询默认是关闭的，如下
```
show variables like 'log_queries_not_using_indexes';
```
![](https://raw.githubusercontent.com/upeng/upeng.github.io/master/image/2655ccd1-69ab-4c54-b7bf-50b01b2b45e5.png) 
需要打开未使用索引的查询，即未使用索引的查询会记录在案
```
set global log_queries_not_using_indexes=on
```
<!-- more -->

### 慢查询时间设置
 查询时间超过多长时间的查询记录会记录到日志中； 默认为慢查询的时间10秒，即 超过10s的查询会记录到慢查询日志中
![](https://raw.githubusercontent.com/upeng/upeng.github.io/master/image/d99d63ee-d0e2-4bfb-8230-984d884b1528.png) 
```
set global long_query_time=0;
```
a litter bug i think：在本窗口查询时发现慢查询时间还是10；但是重新打开一个窗口查询结果才是0
这样设置的目的是：不管什么查询，都会记录到慢查询日志中

### 打开慢查询日志
设置完上述两项后，我们再打开慢查询日志
```
set global slow_query_log=on;
```
输入两个查询语句
```
select * from actor limit 10;
select * from actor where first_name like '%A%';
```
### 打开日志文件
日志文件位置，查看变量slow_query_log_file的值
```
show variables like 'slow_query_log_file';
```
当然，我们也可以手动设置慢查询日志的存放路径及文件名
```
set global slow_query_log_file = ****
```
### 慢查询日志所包含的内容
![](https://raw.githubusercontent.com/upeng/upeng.github.io/master/image/e3f793a8-8b50-464c-b52b-5ffbc78ab187.png) 

1. 执行sql的主机信息
`# User@Host: root[root] @ localhost [127.0.0.1]`
2. sql的执行信息
`# Query_time: 0.001008  Lock_time: 0.000000 Rows_sent: 104  Rows_examined: 200`
3. sql执行时刻
`SET timestamp=1461435221;`
4. sql内容
`select * from actor where first_name like '%A%';`

### 慢查询日志的分析工具
mysqldumpslow输出