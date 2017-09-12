---
title: PHP设计模式-单例模式
date: 2015-10-14 22:21
tags: 设计模式
categories: PHP编程
---

## 单例模式
`场景`:有多个类需要读取数据库，而数据库连接类只需要一个对象就够了。其他的类只需调用这个类的连接对象即可
`问题`：如何获取两个严格相等（全等）的对象（===）
`答案`：只有当两个对象是同一个对象的时候才是全等的！

<!-- more -->

```
//1、用同一个类new两个对象
class Single{}
$s1 = new Single();
$s2 = new Single();
echo $s1 === $s2 ? '是同一个对象' : '不是同一个对象';   //不是同一个对象，尽管他们内部方法变量完全相同

//2、不是同一个对象，主要是因为new了两次，所以下面要封锁new操作
class Single
{
    protected function __construct(){}
}

$s1 = new Single();  

//3、封锁了外部的new操作，同时需要在内部进行new操作

class Single
{
    public static function getIns()
    {
        return new self();
    } 
    protected function __construct(){}
}
$s1 = new Single();
$s2 = new Single();
echo $s1 === $s2 ? '是同一个对象' : '不是同一个对象';
//不是同一个对象！因为内部new了两次！

//4、getIns先判断实例
class Single
{
    protected static $ins = null;
    public static function getIns()
    {
        if (self::$ins === null)
        {
            self::$ins = new self();
        }
        return self::$ins;
    } 
    protected function __construct(){}
}
$s1 = new Single();
$s2 = new Single();
echo $s1 === $s2 ? '是同一个对象' : '不是同一个对象';//是同一个对象

 > 注:**方法前加final，则方法不能被覆盖**，否则当有类继承它的时候会覆盖construct,又不是同一个对象了，**类前加final，则类不能被继承**

//5、禁止覆盖方法和克隆对象
class Single
{
    protected static $ins = null;
    public static function getIns()
    {
        if (self::$ins === null)
        {
            self::$ins = new self();
        }
        return self::$ins;
    } 
    //禁止覆盖方法
    final protected function __construct(){}
    //防止克隆
    final protected function __clone(){}
}

class dbSingle extends Single
{

}
$s1 = new dbSingle();
$s2 = new dbSingle();

$s1 = Single::getIns();
$s2 = clone $s1;
```