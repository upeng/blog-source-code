---
title: python日期格式转换总结
date: 2022-11-07 12:25:14
tags: Python
categories: Python
---
Q：问什么做这个整理？

A：在python项目开发过程中，经常遇到日期时间转换的操作，和php仅两个简单的转换函数不同，python的时间日期库函数较多，语义化不是很清晰，需要对datetime或time库函数非常熟悉才能熟练运用，而且网上随便搜一搜，得到的都是各种案例，没有一种通用的使用说明文档，导致每次都要重复search，效率很低，于是结合部分网友总结及对python时间日期库的查阅，总结了以下相对通用的方法，简单易记，减少浪费过多时间在这上面。

# 日期转换常见场景
* timestamp to string
* string to timestamp

# datetime转换场景
*  timestamp to datetime struct
*  string to datetime struct
*  datetime struct to timestamp
*  datetime struct to string

# 通用转换流程及方法
###  1. timestamp -> datetime/time struct -> string
```
1667555103 -> datetime/time struct -> 2022-11-04 17:45:03 | 20221104 | 2022/11/04 等format
```

> 方法1 datetime组件
- datetime_struct = datetime.datetime.utcfromtimestamp(1667555103)
- string = datetime_struct.strftime("%Y-%m-%d %H:%M:%S")

> 方法2 time组件
- time_struct = time.localtime(1667555103)
- string = time.strftime("%Y-%m-%d %H:%M:%S", time_struct)


### 2. string -> datetime/time struct -> timestamp
```
2022-11-04 17:45:03 | 20221104 | 2022/11/04 等format -> datetime/time struct -> 1667555103
```
> 方法1 datetime组件
- datetime_struct = datetime.datetime.strptime(string, "%Y-%m-%d %H:%M:%S")
- stamp = datetime_struct.timestamp()

> 方法2 time组件
- datetime_struct = datetime.datetime.strptime(string, "%Y-%m-%d %H:%M:%S")
- time.mktime(datetime_struct.timetuple())

# 具体案例

* 昨天此刻，向前/后退n天。 => 时间戳、指定格式
* 昨天12:00，向前/后退n天。 => 时间戳、指定格式



> to 字符串string
```
datetime_struct = datetime.datetme.now() + datetime.timedelta(days=-1)
string = datetime_struct.strftime("%Y-%m-%d %H:%M:%S")
string = time.strftime("%Y-%m-%d %H:%M:%S", datetime_struct.timetuple())
```

> to 时间戳stamp
```
datetime_struct = datetime.datetme.now() + datetime.timedelta(days=-1)
stamp = datetime_struct.timestamp()
```













