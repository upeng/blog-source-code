---
title: 使用zephir快速编写php扩展
date: 2016-06-30 23:42:24
tags: zephir
categories: PHP编程
---

## zephir简介
[zephir][1]开源，是`zend engine / php / intermediate`的缩写，其语法和php很相似，所以对于phper而言创建php扩展简单快捷。如果你不想使用C编写php扩展，可以考虑zephir，因为它很简单，学习成本低。
<!-- more -->
## zephir特点
`dynamic/static` 变量类型可以是动态/静态变量
`pointers or direct memory management aren’t allowed`不允许指针和直接的内存管理
`ahead of time` 编译模型AOT
`task-local garbage collection` 内存模型 Task-Local垃圾回收

> 下面就以一个简单扩展开发为例，总结使用zephir开发php来扩展的流程！

## 安装zephir
1. 需要先安装相关的依赖
2. 使用git的方式获取源代码进行安装
3. 通过运行zephir命令验证安装是否成功
```
$ git clone https://github.com/phalcon/zephir
$ cd zephir
$ ./install-json
$ zephir help
 _____              __    _
/__  /  ___  ____  / /_  (_)____
  / /  / _ \/ __ \/ __ \/ / ___/
 / /__/  __/ /_/ / / / / / /
/____/\___/ .___/_/ /_/_/_/
         /_/
Zephir version 0.7.1b
```

## 初始化扩展
```
zephir init upfriend(upfriend扩展名)
upfriend/
   ext/
   upfriend/
```
## 编写扩展
在`upfriend/upfriend`目录中创建`filter.zep`文件

```
namespace Upfriend;
class Filter
{
    /**     
    * Filters a string returning its alpha characters
    * @param string str     
    */
    public function alpha(string str)
    {
        char ch; string filtered = "";

        for ch in str {
            if (ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z')
            {
              let filtered .= ch;
            }
        }

        return filtered;
    }
    public static function hello()
    {
        echo "hello zephir";
    }
}
```
## 编译扩展
`upfriend`根目录运行`zephir bulid`编译扩展
```
$ zephir build     
Preparing for PHP compilation...
Preparing configuration file...
Compiling...
Installing...
Extension installed!
Add extension=utils.so to your php.ini
Don't forget to restart your web serverp
```
## 使用扩展
编译成功，修改php.ini配置文件，添加一行
```
extension=upfirend.so //upfriend.so与php.ini位于同一目录
php -m  //查看扩展是否安装成功
```
在php中使用这个类（扩展）的方法
```
<?php
    $filter = new Upfriend\Filter();
    echo $filter->alpha("01he#l.lo?/1"); // prints hello
    Upfriend::hello() // prints hello zephir
```

[1]: http://zephir-lang.com/




