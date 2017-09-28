---
title: 经常使用到的几个命令行linux&php
date: 2016-11-13 23:24:35
tags:
  - nc
  - linux
  - php
categories:
  - PHP
---

### [](#nc命令使用方法 "nc命令使用方法")nc命令使用方法

同一局域网（公司内网）下经常会进行文件的传输，使用`nc`命令非常方便。

A向B发送文件，A端命令
A@localhost:~ nc B的ip 约定端口 < 传输文件名
B端命令，先处于listen状态
B@localhost:~ nc -l 约定端口 > 传输文件名

example

<!-- more -->

```
机器A 192.168.1.100
机器B 192.168.1.101

机器A发送文件（test.txt）到机器B
A@localhost:~ nc 192.168.1.101  8888 < test.txt 

机器B监听8888端口（自定义）
B@localhost:~ nc -l 8888 > test.txt

```

### [](#php经常使用到的命令行 "php经常使用到的命令行")php经常使用到的命令行

*   查看php文件是否有语法错误
    `php -ln filename.php`

*   执行php文件
    `php filename.php`
    `php -f filename.php`

*   搜索php.ini关键词信息
    `php -i | grep keyword`

*   查询php.ini位置信息
    `php --ini`

*   查看php扩展模块
    `php -m`

> 上述`查询php.ini`和`检测语法错误`很好用，源于查看session配置和运行脚本没有任何输出。

备注：关于git命令，特别是与远程仓库的交互，待整理和总结