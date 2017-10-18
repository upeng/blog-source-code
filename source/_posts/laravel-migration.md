---
title: laravel migration
date: 2016-05-07 20:10
tags: laravel
categories: PHP
---

## laravel migration工具
 
### 新建表`articles`步骤
 
* migrations下生成`2015_12_13_104637_create_articles_table.php`文件
`php artisan make:migration create_articles_table --create=articles`
* 修改生成的文件`2015_12_13_104637_create_articles_table.php`

```
Schema::create('articles', function (Blueprint $table) {
    $table->increments('id');
    $table->string('title');
    $table->text('content');
    $table->timestamp('published_at');
    $table->timestamps();
});
```

* 保存文件，执行下面命令，数据库中生成articls表
`php artisan migrate`

### 删除刚才创建的table
```
//回退操作
php artisan migrate:rollback
```
<!-- more -->
### 增加表字段

1. 如果将table drop后在create中添加字段,会删除表中已有数据。

2. 在migrations下新建文件`add_intro_into_articles`
```
php artisan make:migration add_intro_into_articles --table=articles

```

3. 修改add_intro_into_articles
```
public function up()
{
    Schema::table('articles', function (Blueprint $table) {
        $table->string('intro');
    });
}

public function down()
{
    Schema::table('articles', function (Blueprint $table) {
        $table->dropColumn('intro');
    });
}
```

* 注意关键字的不同，导致生成的文件中匿名函数书写的不同
```
--create=tablename
Schema::create('articles',function(){});

--table=tablename
Schema::table('articles', function(){});
```
