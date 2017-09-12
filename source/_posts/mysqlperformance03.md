---
title: mysql数据库之索引优化
date: 2016-04-27 00:44:33
tags: mysql
categories: MySQL
---

## 如何选择合适的列建立索引?
1. 在where从句、group by从句、order by从句、on从句中出现的列
2. 索引字段越小越好
3. 离散度大的列放到联合索引的前面

> 理解离散度
```
SELECT * FROM payment where staff_id = 2 AND customer_id = 584;
```

> 是index(staff_id, customer_id)好还是index(customer_id, staff_id)好？

**备注**：比较staff_id和customer_id两者的离散度，那个离散度高，那个列就放置到联合索引的前面
<!-- more -->

> 计算离散度：

```
SELECT count(distinct staff_id), count(distinct customer_id) FROM payment;
```

## 索引优化SQL的方法
### 索引的维护及优化---重复及冗余索引
> 重复索引

重复索引是指相同的列以相同的顺序建立同类型的索引，如下表中primary key 和ID列上的索引就是重复索引
```
create table test(
    id int not null primary key,
    name varchar(10) not null,
    titile varchar(50) not nullm,
    unique(id)
)engine=innodb;
```

> 冗余索引

冗余索引是指多个索引的前缀列相同，或是在联合索引中包含了主键的索引，下面这个例子中key(name, id)就是一个冗余索引
```
create table test(
    id int not null primary key,
    name varchar(10) not null,
    titile varchar(50) not nullm,
    key(name, id)
)engine=innodb;
```

> 工具检查重复及冗余索引

```
pt-duplicate-key-checker -uroot  -p 123 -h 127.0.0.1
```

### 索引的维护及优化---删除不用索引
 目前mysql中还没有记录索引的使用情况，但是在perconMySQL和MariaDB中可以通过INDEX_STATISTICS表来查看哪些索引未使用，但是在MySQL中目前只能通过慢查询日志配合pt-index-usage工具来进行索引使用情况的分析
```
pt-index-usage -uroot -p123 mysql-slow.log
```


