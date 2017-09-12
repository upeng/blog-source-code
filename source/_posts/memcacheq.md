---
title: MCQ学习笔记
date: 2016-07-11 23:29:55
tags: memcacheq
categories: 其他
---
初次接触MCQ源于蓝v，之前也听同事说微博的消息推送也是使用MCQ。认识MCQ之前感觉它很神秘，用之后才发现并也不过如此，所以很多知识不能只凭想象或者言传，需要自身实践才可知晓。这里想记录一下自己使用MCQ的过程，以及MCQ的使用场景、优缺点等等。

> MemcacheQ - Simple Queue Service over Memcache
在memcache基础上的简单队列服务，所以它兼具memcache和队列的特点。

<!-- more -->
## Features[特性]
* damn simple（炒鸡简单)
* very fast（非常快）-> 给予memcache,在内存中操作
* multiple queue（多队列)
* concurrent well（并发good)
* memcache protocol compatible（兼容memcache协议)

memcacheq(MCQ)是轻量级的消息队列，依赖于Berkeley DB和libevent。Berkeley DB用于持久化存储队列数据，避免MCQ宕机数据丢失。
## 使用场景
> 高并发，数据可以弱一致性（弱一致性？）。

蓝v后台沿用之前橙v逻辑（数据量大，并发高），也使用了MCQ。在高并发，大数据量的情况下，请求过多会发生阻塞，试想，在这种情况下，如果大量请求同时对mysql进行增删改，则会产生很大行锁（InnoDB）或表锁（Myisam）;请求堆积过多还可能会出现too many connections的错误。使用了MCQ，可以异步处理，怎么异步法？请求在不断的往队列塞（入队列），队列也不断地去消费这些请求（出队列），当然消费能力也是在mysql能力范围之内的，为啥？【思考缓冲作用】二者就处于异步状态。

支持多队列，比如我们创建了队列A，B，C，他们可以同时工作，其实这也体现了并发异步的特性。


## 使用方法
因为MCQ是基于memcached开发，所以可以使用memcached的命令对它进行各种操作；客户端比如php可以像操作memcacheq那样操作MCQ
### 启动MCQ
`/usr/local/memcacheq/bin/memcacheq -d -r 1024 -l 127.0.0.1 -p 22201 -u root`

**参数说明**
`-d` 以守护进程方式运行
`-r` 最大化核心文件限制
`-l` 监听网卡
`-p` TCP监听端口
`-u` 以（root）身份运行

### 最常用的mcq命令：set和get
```
telnet 127.0.0.1 22201 ....

set testqueue 0 0 3    //队列添加一条消息
xxx
STORED

set testqueue 0 0 5
xxxxx
STORED

stats queue
STAT testqueue 2/0

get testqueue
VALUE testqueue 0 3
xxx
END

stats queue
STAT testqueue 2/1
```
**注意**：mcq没有expire time这个标记，是因为mcq支持数据持久化（Berkeley DB）。另外mcq存储长度length有限制（蓝v踩到的坑），之前同步前台用户认证申请材料至后台，因为材料增多，导致参数长度增加，超过了mcq的消息长度的限制：limit ? 后面分析查明

> MCQ和Redis的比较

**memcacheq**

* 数据保存在内存中，速度快
* 支持数据持久化
* 队列长度限制小
* 与memcache兼容
* 与memcached是两个独立服务，如同时使用需要分别安装

**Redis**

* 数据保存在内存中，速度快
* 支持数据持久化
* 队列长度限制小
* 支持多种数据类型
* 同时可以做为缓存来使用

自己思考一下，在项目中MCQ和Redis各自的使用场景

后续阅读文章
[深刻理解Linux进程间通信（IPC）][1]
[可靠消息队列浅谈][2]
[MemcacheQ分析][3]


  [1]: https://www.ibm.com/developerworks/cn/linux/l-ipc/
  [2]: http://blog.buaa.us/talk-about-mq/
  [3]: http://www.fireflies.me/tag/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97/