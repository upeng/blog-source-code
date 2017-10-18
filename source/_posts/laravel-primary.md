---
title: laravel初体验
date: 2016-04-25 20:10
tags: laravel
categories: PHP
---

### 访问流程

> 路由文件routes.php

```
/*默认是以匿名函数的方式进行路由,/是当前域名下加载视图文件welcome.blade.php */

Route::get('/', function () {
    return view('welcome');
});
Route::get('/about', function () {
    return view('about');
});
Route::get('/blog', function () {
    return view('blog.content');  //其中的“.”可以用“/”代替
});

//如果使用非匿名函数的方式

Route::get('/', 'UserController@index');
Route::get('/about', 'UserController@about');
Route::get('/blog', 'UserController@blog');
```
### 创建控制器
> 1. 可以手动创建Controller，也可以使用命令行创建控制器`php artisan`工具
> 2. 参数 --plain使用则不会生成一些默认的方法如index，create等

```
php artisan make:controller UserController
php artisan make:controller TestController --plain

```
### 视图的加载
> 加载视图的方法

``` 
return view('welcome');
return view('blog/title');
return view('blog.content');
```
> 怎么向blade视图传递变量

```
$name = '<p style="color:red">upfriend</p>';
$time = date('Y-m-d', time());
$arr = array('first' => 'jerry', 'last' => 'bool');

//传递变量-通用方式传递给$data数组
$data['name'] = $name;
$data['time'] = $time; 
return view('blog.content', $data);

//Laravel链式操作方式 
return view('blog.content')->('name' => $name);  //传递一个变量
return view('blog.content')->with(array('name' => $name, 'time' => $time));  //传递数组变量

//compact方法
return view('blog.content', compact('name', 'time', 'arr'));  //去掉$的变量名
```

> 如何正确输出变量的值

```
//原生PHP输出数据方式，默认会对变量中的字符会进行转义（xss?）
<h1><?=name?></h1>   

//Laravel的blade模板引擎渲染方式，两个大括号{{}}表示转义后输出
<h1>{{name}}</h1>  //浏览器输出<p style="color:red">upfriend</p>

<h1>{!!name!!}</h1>  //会见变量作为html,输出红色字体的upfriend
```









