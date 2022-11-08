---
title: Redis常用命令学习总结
date: 2018-03-26 11:58:25
tags: redis
categories: Redis
---

> redis命令总结
http://redisdoc.com/index.html

# 场景1：字符串
redis的key-value结构中，value是字符串，起缓存作用，可设置过期时间，比如session，适用于读比较频繁的场景

常用命令
```
SET key value
GET key
SETEX key seconds value
PSETEX key milliseconds value
SETNX key value
MGET key [key ...]
MSET key value [key value ...]
INCR key(INCRBY key increment)
DECR key(DECRBY key decrement)
```

# 场景2：哈希表
redis的key-value结构中，value是哈希表结构，那什么是哈希表呢？

哈希表（Hash table，也叫散列表），是根据关键码值(Key value)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。 Hash表是一种特殊的数据结构，它同数组、链表以及二叉排序树等相比较有很明显的区别，它能够快速定位到想要查找的记录，而不是与表中存在的记录的关键字进行比较来进行查找。这个源于Hash表设计的特殊性，它采用了函数映射的思想将记录的存储位置与记录的关键字关联起来，从而能够很快速地进行查找。

在redis中的使用场景，比如系统只分配一个redis的key给我们，现在需要存储各个用户在每条的访问量；key可以设置成日期相关，value是各个用户及其访问量；

```
key:  pv_20180323
value:
tayloryu 80
xiaoming 90
zhangsan 100
```
还比如每条数据上报的错误量，发送量，接收量，按天，按小时，按分钟设置成field，像目前的模调、Agent业务
<!-- more -->
## 常用命令

`HSET key field value` 将哈希表 key 中的域 field 的值设为 value 。 如果 key 不存在，一个新的哈希表被创建并进行 HSET 操作。 如果域 field 已经存在于哈希表中，旧值将被覆盖。

`HGET key field` 返回哈希表 key 中给定域 field 的值。

`HGETALL key` 返回哈希表 key 中，所有的域和值。 在返回值里，紧跟每个域名(field name)之后是域的值(value)，所以返回值的长度是哈希表大小的两倍。

`HMSET key field value [field value ...]` 同时将多个 field-value (域-值)对设置到哈希表 key 中。

`HMGET key field [field ...]` 返回哈希表 key 中，一个或多个给定域的值。 如果给定的域不存在于哈希表，那么返回一个 nil 值。 因为不存在的 key 被当作一个空哈希表来处理，所以对一个不存在的 key 进行 HMGET 操作将返回一个只带有 nil 值的表。

`HSCAN key cursor [MATCH pattern] [COUNT count]`

`HSETNX key field value` 将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在。 若域 field 已经存在，该操作无效。 如果 key 不存在，一个新哈希表被创建并执行 HSETNX 命令。

`HKEYS key` 返回哈希表 key 中的所有域。

`HVALS key` 返回哈希表 key 中所有域的值。

`HLEN key` 返回哈希表 key 中域的数量。

`HEXISTS key field` 查看哈希表 key 中，给定域 field 是否存在。

`HDEL key field [field ...]` 删除哈希表 key 中的一个或多个指定域，不存在的域将被忽略。
