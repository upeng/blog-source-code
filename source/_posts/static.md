---
title: 静态方法和静态成员变量
date: 2016-10-12 23:29:13
tags:
  - static
categories:
  - PHP
---

> 问题来源：

写了一个`Model_User`，创建了2个静态方法func1和func2，2个静态方法中都含有公共的连接数据库的逻辑，突然想到代码重用，然后创建了构造方法，将连接数据库的逻辑放在`__construct()`中，然后去调用`Model_User`的静态方法
<!-- more -->

```
Model_User::func1();

```

`报错：`

```
Creating default object from empty value。。。
Using $this when not in object context in。。。。

```

因为我们直接使用类调用静态方法的方式，是根本不会走到构造方法的，而构造方法只有在new实例的时候才会调用。同时因为`$this`本身就是一个instance（实例）而我们根本就没有new 对象，所以。。。

**注意**
1.  如果使用类调用静态方法的形式，那么要注意`__construct`里面的操作就over了，因为`__construct`不会调用；
2.  不能在静态方法中使用`$this`去调用普通成员变量和成员函数，因为你没有实例化对象，而$this就是一个instance;就算这样也是不可行的

```
$obj = new Model_User
$obj->func1();

```

> 正常情况下，静态方法中是不能调用非静态成员变量或非静态方法的！

静态成员变量和静态方法存储在堆中(一个公共的池子)。
所有实例化的对象instance都可以使用和操作它。
举例子：

```
class Apple
{
    public static $a = 1;
    public $b = 1;

    public function set(2)
    {
        self::$a = $num; 
        $this->b = $num;
    }

    public function get()
    {
        return array(self::$a, $this->b);
    }
}

$apple1 = new Apple();
$apple1->set(2);
$res1 = $apple1->get();

$apple2 = new Apple();
$res2 = $apple2->get();

print_r($res1);  //array(2,2)
print_r($res2);  //array(2,1)

```

很明显，静态成员变量和普通成员变量的区别；在实际应用场景中，需要合理使用