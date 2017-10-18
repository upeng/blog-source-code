title: Eloquent ORM多个and和or条件查询
tags:
  - laravel
  - eloquent
categories:
  - PHP
author: tayloryu
date: 2017-09-14 22:38:00
---
### Eloquent ORM和查询构造器Query Builder
```
Model::all();
Model::where();
Model::groupBy()->select('field_name')->get()->toArray()
```
之前一直使用ORM，后面可以尝试使用查询构造器，ORM相比查询构造器，不需要指定table_name和field等信息，但是也有局限性，比如我们经常会需要查询指定字段field
对于查询构造器而言是十分方便的select字段名接近原生查询方法，易理解易记忆。
<!-- more -->
```
$users = DB::table('users')->select('name', 'email')->get();
//那么ORM是否无法查询指定字段呢？当然不是,ORM也是可以滴
Market::all([*])
//将Model类作为一个obj，然后链式操作
$market = Marketer::query(); 
//根据条件选择性地执行下面的条件
$market->where();
```

查询构造可以指定tablename为某个Model的static变量,便于维护。当然查询构造器有很多很好用的查询方式，而且链式操作强悍，实例：[查询构造器](https://cs.laravel-china.org/#db)



### 踩到的一个坑
> 多个or和and条件查询

```
select * from users where (id=2 or name='taylor' or age=27) and address='shanghai'

Users::where(id, 2)
    ->orWhere(name, 'taylor')
    ->orWhere(age, 27)
    ->where(address, 'shanghai')
```

发现结果和预期不同，因为`and`逻辑会先于`or`，所以会出现问题，那么如何**按照我们指定的顺序**去查询？官方手册并未这么详细，在Stack Overflow找到了答案：
[Laravel 5 Eloquent where and or in Clauses](https://stackoverflow.com/questions/30434037/laravel-5-eloquent-where-and-or-in-clauses)

```
$market->where( {
Users::where(function($q){
    $q->where('id', 2)
    ->orWhere('name', 'taylor')
    ->orWhere('age', 27)
})->where('address', 'shanghai');


继续，还有坑，因为我们传递的都是数值和字符串，正常情况是变量

$id = 2;
$market->where( {
Users::where(function($q){
    $q->where('id', $id)
    ->orWhere('name', 'taylor')
    ->orWhere('age', 27)
})->where('address', 'shanghai');

```

`Error: $id is not definied`
`Reason:` 匿名函数内部的`where`和`orWhere`是一起的（括号），但是在匿名函数内是取不到其外部的变量的，可以在`function`内部传参数解决，也可以将变量赋值成成员变量解决；
比如`$this->id = 2；`这样可以 将`$id` 改成`$this->id`