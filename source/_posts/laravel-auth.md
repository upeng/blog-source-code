---
title: laravel学习笔记-用户认证
date: 2016-05-29 08:17:07
tags: laravel
categories: PHP编程
---

在平时开发过程中，用户登录注册是非常常见的场景，比如大部分后台管理、论坛等等；用户一般web访问流程从业务来说是这样的：
<!-- more -->
1. 注册
2. 登录
3. 进入首页
4. 查看&操作
5. 退出登录

很多时候为了实现1、2、5这部分功能，重复造了很多轮子。目前接触到的laravel，就避免了你重复造轮子的问题，下面结合自己在laravelacademy上学习的laravel认证这部分做一下记录和总结，我学习的思路大致上是先学会用，然后搞清楚原理；所以这篇blog先记录如何使用的，快速实现function，尽管写的很low，但是给自己看，最关键的是是一个温习的过程，fighting！

laravel认证【插件】做了什么？

* 注册
* 登录
* 找回密码（这都帮忙做好了^_^）

###  数据库表准备
注册其实是入库的过程，也就是说，你需要有一个表专门用以存储注册用户的信息，laravel的`migration`已经自动创建了`users`和`password_resets`建表语句，只需`php artisan migrate`即可创建2张表到数据库；况且Model层已经创建了`User.php`
<!-- more -->
### 路由设置
```
// 认证路由...
Route::get('auth/login', 'Auth\AuthController@getLogin');
Route::post('auth/login', 'Auth\AuthController@postLogin');
Route::get('auth/logout', 'Auth\AuthController@getLogout');

// 注册路由...
Route::get('auth/register', 'Auth\AuthController@getRegister');
Route::post('auth/register', 'Auth\AuthController@postRegister');

// 发送密码重置链接路由
Route::get('password/email', 'Auth\PasswordController@getEmail');
Route::post('password/email', 'Auth\PasswordController@postEmail');

// 密码重置路由
Route::get('password/reset/{token}', 'Auth\PasswordController@getReset');
Route::post('password/reset', 'Auth\PasswordController@postReset');

//成功认证后网站首页
Route::get('/', 'UserController@index');
```
你会发现`AuthController`没有`getLogin、postLogin`这些方法，这里需要说明一下[trait][1]官方手册讲述的已经很清楚，顺便学习一下overtrue的这篇文章[我所理解的 PHP Trait][2]，所以如果想知道auth怎么操作数据库，又是如何加载视图，做何种跳转，深入trait层去看代码就好。


### 控制器
laravel已经创建好了auth控制器，位于`Controllers/Auth/`下，而且中间件也都默认创建，基本的准备工作已经做足，只需你创建view，做一些简单的设置就可以使用了

#### 部分属性设置
注册登录和密码重置的路由入口都在`Controllers/Auth/`下的`AuthController.php`和`PasswordController.php`,他们都是用了`trait`
如果你在控制器中没有定义一些path，`trait`底层都会使用自己定义的默认值，如果你喜欢这个默认值，你就按照他来写你的`route.php`吧，这里我们可以自己定义，覆盖`trait`中的这部分属性；如果不知道属性名称，进入`trait`查看
> eg:
```
//in AuthController.php
protected $redirectPath = '/';  //登录或注册成功进入首页，默认是/home
protected $redirectAfterLogout = '/auth/login'; //退出登录后跳转到登录页

//in PasswordController.php
protected $redirectPath = '/';  //密码重置成功后跳转
protected $subject = '点击此处重置密码：';  //邮件内容
```



#### 发挥认证作用
比如在登录成功后跳转的路由'/'，我们在`UserController`中怎么确定用户是否登录，如果没有登录怎么让用户成功登录后callback到其输入的地址；**auth中间件开始发挥其作用了**，所谓中间件，从字面上理解就是在用户访问该页面前，先去执行中间件内容。其实在Auth中也使用了中间件guest;在UserController中加入下面这个构造函数，即可让用户先登录再访问。
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
    }
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index(Request $request)
    {
        $user = $request->user();
        echo $user['name'] . '登录成功！';
    }
}

```

### 发散
#### 关于模板
使用Form表单类，加上bootstrap打造属于自己的登录注册界面，模板文件名源于trait中定义的view，当然我们可以重写getLogin等方法，因为它们有的简单到只有一个`return veiw(*.blade.php)`
主要创建以下模板：[详细页面][3]

* register.blade.php
* login.blade.php
* password.blade.php
* reset.blade.php

#### 关于设置session
我们常见的登录登出按钮，重置密码，获取当前登录用户的基本信息等等。开始搞起吧！
后面仔细研究源码，从根本上掌握auth认真的全流程。



  [1]: http://php.net/manual/zh/language.oop5.traits.php
  [2]: http://overtrue.me/articles/2016/04/about-php-trait.html
  [3]: https://github.com/upeng/learn-laravel