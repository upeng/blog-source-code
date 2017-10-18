---
title: PHP设计模式-工厂模式
date: 2015-10-12 22:12
tags: 设计模式
categories: PHP
---

## 简单工厂模式
直接上代码，直观了解工厂模式！
```
<?php
//接口，是客户端和服务端共同遵守的开发规范
interface db
{
    public function conn(){};
}

//服务端
class Dbmysql implements db
{
    public function conn()
    {
        echo 'connected the mysql';
    }
}

class Dbsqlite implements db
{
    public function conn()
    {
        echo 'connected the sqlite';
    }
}

//客户端 
$mysql = new Dbmysql();
$mysql->conn();

$sqlite = new Dbsqlite();
$sqlite->conn();

//如果服务端不想让客户端知道自己内部类的实现细节，统一一个固定的入口，这样就是我们所说的
//使用方知道的越少越好！将上述两个类封装起来

//服务端，开发接口Dbconnect::type(@param)

class Dbconnect 
{
    public static function type($type)
    {
        if ($type == 'mysql')
        {
            return new Dbmysql();
        }
        else if ($type == 'sqlite')
        {
            return new Dbsqlite();
        }
        else
        {
            throw new Exception("Error type is not exist", 1);
            
        }
    }
}

//客户端
$mysql = Dbconnect::type('mysql');
$mysql->conn();

$sqlite = Dbconnect::type('sqlite');
$sqlite->conn();
```
在我们使用围脖的接口时，和这个有点类似，一个业务需求给你一个方法如showbatch(),friendship(),show()；当然他们是某个类的静态方法，我们不需要知道它是如何具体实现的，只要知道，当我们传递合适的参数，使用这个接口，就可以获得自己想要的数据即可

` Q:现在如果需要连接oracle、PDO、SqlServer，怎么办？当然php可以直接修改服务端的代码，增加else if，但是对于java,c++而言，如果修改代码，还需要再次编译，这是非常耗时的!`

 > **在面向对象设计（OOD）设计法则中，重要的开闭原则：对于修改是封闭，对于扩展是开放的**

## 工厂方法 
**注意开闭原则**
//**服务端**
```
interface Factory
{
    public function createDb(){};
}

class mysqlFactory implements Factory
{
    public function createDb()
    {
        return new Dbmysql();
    }
}

class sqliteFactory implements Factory
{
    public function createDb()
    {
        return new Dbsqlite();
    }
}

//**客户端**
//由于开放了两个接口，一个是创建数据库，一个是连接数据库

$fact = new mysqlFactory();
$db = $fact->createDb();
$db->conn();

$fact = new sqliteFactory();
$db->$fact->createDb();
$db->conn(); 

//现在如果新增了oracle数据库连接，就可以扩展oracle，不需要修改代码，扩展如下：

class Dboracle implements db()
{
    public function conn()
    {
        echo 'connected oracle database';
    }
}
class oracleFactory implements Factory
{
    public function createDb()
    {
        return new Dboracle();
    }
}


$fact = new oracleFactory();
$db = $fact->createDb();
$db->conn();
```