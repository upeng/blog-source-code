---
title: laravel的blade视图模板引擎
date: 2016-04-29 00:18
tags: laravel
categories: PHP编程
---

### blade模板引擎使用
很多时候，我们的view模板有很多重复的地方，比如head、footer部分，当然可以定义一些共用的部分，置于common文件夹下，然后使用include将其引入进来。而blade有一套自己的策略

比如定义一个`common.blade.php`文件，如下：
```
<!DOCTYPE html>
<html>
    <head>
        <title>Laravel</title>
        <link href="https://fonts.googleapis.com/css?family=Lato:100" rel="stylesheet" type="text/css">
        <style>
            html, body {height: 100%;}
            body {margin: 0;width: 100%;display: table;font-weight: 100;font-family: 'Lato';}
            .container {text-align: center;display: table-cell;vertical-align: middle;
            }
            .content {text-align: center;display: inline-block;}
            .title {font-size: 96px;}
            .footer{color: green;font-size: 100px;}
        </style>
    </head>
    <body> 
        <div class="container">
            @yield('content')
            @yield('footer')
        </div>
    </body>
</html>

```
<!-- more -->
* 其中，`@yield('content')`和 `@yield('footer')`是根据不同模板的需求进行动态设置与填充的
*  `welcome.blade.php`和`contact.blade.php`模板与`common.blade.php`样式相近，只是内容不同，或者后者内容更多。他们均可以继承`common.blade.php`的所有内容，需要content则可以自定义显示，需要footer可以自定义显示footer
* 示例如下

```
# welcome.blade.php
@extends('common')      //继承common.blade.php
@section('content')     //开始
    <div class="content">
        <div class="title">Laravel 5 -welcome</div>
    </div>
@stop                   //结束


# contact.blade.php
@extends('app')
@section('content') 
    <div class="content">
        <div class="title">contact 825415473@qq.com</div>
    </div>
@stop
@section('footer')
    <div class="footer">
        Laravel 5
    </div>
@stop 

```
### blade中的条件判断和循环操作
```
# 条件判断和循环操作，通用方法是在<?php **** ?>
# blade有自己的语法
@extends('app')
@section('content')

@if($condiction == 'bool')
    <h1>{!!$name!!}</h1>
@else
    <h1>{{$time}}</h1>
@endif

<h2>
    @foreach($arr as $key => $value)
        {{$value}}
    @endforeach
</h2>

@stop
```

