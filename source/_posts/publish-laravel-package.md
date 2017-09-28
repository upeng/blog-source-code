---
title: 从编写到发布laravel扩展
date: 2016-10-11 23:08:11
tags: 
  - laravel
  - composer
  - packageist
categories: PHP

---

### [](#工具准备 "工具准备")工具准备

> 创建开源项目三工具github、packageist、composer

```
 github: git仓库，是用于存放源代码的地方
 packagist: 用于发布github上的扩展程序或者项目
 composer: 用于使用代码的工具

```

<!-- more -->

`github->packagist->composer`

1.  怎样写一个laravel扩展，发布到packagist，提供composer方法
2.  git命令熟悉

### [](#写laravel扩展 "写laravel扩展")写laravel扩展

#### [](#本地编写 "本地编写")本地编写

本地编写package，完成后，在laravel根目录下的composer.json中添加一行
`"Upstriving\\Curd\\": "package/upstriving/curd/src"`

```
 "autoload": {
        "classmap": [
            "database"
        ],
        "psr-4": {
            "App\\": "app/",
            "Upstriving\\Curd\\": "package/upstriving/curd/src"
        }
    },

```

添加之后`composer dump-autoload`将之加到路由中
`php artisan tinker`测试

```
>>> app('sqladmin')
=> Upstriving\Curd\SQLAdmin {#629}
>>> app('sqladmin')->makeSelect('user','id,name',['id'=>2]);
=> [
     "sql" => "select id,name from user where id=?",
     "data" => [
       2,
     ],
   ]
>>> app('sqladmin')->makeDelete('user',['name'=>'upfriend'])
=> [
     "sql" => "delete from user where `name`=?",
     "data" => [
       "upfriend",
     ],
   ]

```

#### [](#生成composer-json "生成composer.json")生成composer.json

```
composer init
This command will guide you through creating your composer.json config.

Package name (<vendor>/<name>) [yupeng6/curd]: upstriving/curd
Description []: assemble sql statements quickly
Author [upeng <825415473@qq.com>, n to skip]:
Minimum Stability []: dev
Package Type (e.g. library, project, metapackage, composer-plugin) []:
License []: MIT

Define your dependencies.

Would you like to define your dependencies (require) interactively [yes]? no
Would you like to define your dev dependencies (require-dev) interactively [yes]? no

{
    "name": "upstriving/curd",
    "description": "assemble sql statements quickly",
    "license": "MIT",
    "authors": [
        {
            "name": "upeng",
            "email": "825415473@qq.com"
        }
    ],
    "minimum-stability": "dev",
    "require": {}
}

Do you confirm generation [yes]? yes

```

#### [](#生成composer-json-1 "生成composer.json")生成composer.json

> 添加autoload
> 
> ```
> "autoload": {
>     "psr-4": {
>          "Upstriving\\Curd\\": "src/"
>     }
> }
> 
> ```

#### [](#推送到github "推送到github")推送到github

> 当前扩展目录下执行

```
git init
git add .
git commit -m 'add new package'

```

github手动创建一个`repository(laravel-sqladmin)`
在本地目录下，复制两行命令并执行，将本地代码推送到github

```
git remote add origin git@github.com:upeng/laravel-sqladmin.git
git push -u origin master

```

新增readme.md

#### [](#通过github的package服务发布到packagist中 "通过github的package服务发布到packagist中")通过github的package服务发布到packagist中

供其他开发者通过composer使用
登录`https://packagist.org/`,
![此处输入图片的描述](http://ww2.sinaimg.cn/mw690/006iFiRLjw1f8o4ehp6l9j31c30kejve.jpg)
拿到token值，回到`github/larvavel-sqladmin/settings/integrations & service`
执行`add service/packagist`
![此处输入图片的描述](http://ww2.sinaimg.cn/mw690/006iFiRLjw1f8o4eibz58j31j50u9gsy.jpg)
输入用户名user和token(从packagist中获取)
成功之后如下
![此处输入图片的描述](http://ww3.sinaimg.cn/mw690/006iFiRLjw1f8o4eizjdvj31k70ra7bl.jpg)

再次回到packagist中，submit刚才那个repository地址
![此处输入图片的描述](http://ww3.sinaimg.cn/mw690/006iFiRLjw1f8o4ejjp7zj30wk0d8jsx.jpg)
![此处输入图片的描述](http://ww2.sinaimg.cn/mw690/006iFiRLjw1f8o4ek6aagj30wp0epmzr.jpg)
![此处输入图片的描述](http://ww1.sinaimg.cn/mw690/006iFiRLjw1f8o4ektd7qj318o0hg77p.jpg)

提示这个package is not auto_updated，需要到github中设置一下
github/larvavel-sqladmin/settings/integrations & service/packagist
点击test service，
![此处输入图片的描述](http://ww3.sinaimg.cn/mw690/006iFiRLjw1f8o4elc6eej31600q6wiv.jpg)

如果显示
`Okay, the test payload is on its way.`
说明自动更新没有问题，再次回到packagist发现刚刚的提示已经不见了
![此处输入图片的描述](http://ww3.sinaimg.cn/mw690/006iFiRLjw1f8o4em26svj30n10d5myx.jpg)

#### [](#修改或新增文件，观察是否实时更新到packagist "修改或新增文件，观察是否实时更新到packagist")修改或新增文件，观察是否实时更新到packagist

> 新增readme.file
> 
> ```
> git add .
> git commit -m ''
> git push
> 
> ```
> 
> 增加版本号

`git tag 1.0 -a`
输入: release version 1.0
`git push --tags`
发现github上多了一个版本号,packagist版本也随之更新
![此处输入图片的描述](http://ww3.sinaimg.cn/mw690/006iFiRLjw1f8o4emqsblj312x0nv446.jpg)