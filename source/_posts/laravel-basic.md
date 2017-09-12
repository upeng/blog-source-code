---
title: 'laravel基础任务学习清单'
date: 2016-08-15 22:24:32
tags: laravel
categories: PHP编程
---


## 关于Restful路由
`Route::resource('task', 'TaskController');`
产生多条路由，`POST/DELTE/PUT/GET`，对应着`TaskController`的各个方法；
对于laravel5.2，使用 `php artisan make:controller TaskController --resource`可产生默认的多种方法
通过`php artisan route:list`可以显示已经注册的路由列表

![laravel-route-list][1]

<!-- more -->

## 表单验证
> store、update方法常使用

基地控制器`Controller`内使用了`ValidatesRequests`这个`trait`，因此他为所有的控制器都提供了方便的`validate`验证方法。

> 调研了解trait机制，为什么TestController use了这个trait，TestController 就可以使用ValidatesRequests的validate方法

### 最原始的表单验证（Controller中）
validate方法会验证接受HTTP传入的请求与验证规则，如果验证通过，我们的代码才会继续向下运行。如果验证失败会抛出异常错误返回给用户。

```
public function store(Request $request)
{
	//验证用户请求
	$this->validate($request,[
		'name' => 'required',
		'content' => 'required|min:5',
	]);

	//通过验证，保存至数据库
	Task::create($request);
}
```

**说明**：我们将验证规则传递到`validate`方法中，如果验证失败，则自动生成一个对应的响应。并将验证的错误提醒闪存至`session`，也就是`$errors`。当验证失败时，用户将被重定向到我们的控制器 `create`方法，让我们在视图中显示错误的消息：
```
@if (count($errors) > 0)
	<div class="alert alert-danger">
		<ul>
			@foreach ($errors->all() as $error)
			<li>{{$error}}</li>
		</ul>
	</div>
@endif
```

**补充**：正常情况下我们想要把错误显示在对应的字段旁边，而不是一起显示在一个列表中；我们打印`session`中的`$errors->all()`是数字索引数组;


> 要查看特定字段的第一个错误消息【比较常见的场景】

**方法如下：**
```
@if ($errors->has('name'))
$errors->first('name');
@endif

@if ($error->has('content'))
$error->first('content');
@endif

```

> 我们想要得到一个字段对应的所有error

**方法如下**：
```
foreach ($error->get('content') as $message)
{
	//
}
```

### 表单请求验证，自定义请求类
> php artisan make request TaskFormReuest;

这时会在`Http/Requests`目录生成`TaskFormReuest.php`,修改`rules`方法，
```
//权限判断
public function authorize()
{
    return true;
}

//验证规则
public function rules()
{
    return [
        'name' => 'required|min:5',
    ];
}

//也可以自定义message方法
public function messages()
{
    return [
        'title.required' => '标题是必填的',
        'body.required'  => '消息是必填的',
    ];
}

//messages还可以这样动态设置
public function messages()
{
	return [
		'required' => ':attribute 是必填字段',
		'min'	=> ':attribute 长度不小于 :min',
	];
}
```

### 手动创建验证程序
不使用validatesRequests的validate方法
我们可以use Validator类，手动创建一个validator实例，并通过Validator::make()生成一个新的validator

```
use Validator;
...
public function store(Request $request)
{
	$validator = Validtor::make($request->all(),[
		'name' => 'requeire',
		'content' => 'require|min:6',
	]);

	if ($validator->fails())
	{
		return redirect()->action('TaskController@index')
		->withErrors($validator)
		        ->withInput();
	}
}
```

**注**：`Validator::make($param1, $param2)`，其中`$param1`是验证的数据，`$params`是规则


## model操作

增：
```
1. Task::create($request);
2. 
$task = new Task();
$task->name = $request->name;
$task->created_at = Carbon::now();
$task->save();
```
删：
```
1.Task::destroy($id);
2.Task::findOrFail($id)->delete();
```

改:
```
Task::update($request, $id);
```

查：
```
1.Task::all();
2.Task::where()->get();
3.Task::orderBy('created_at', 'desc')->get();
```

## factory工厂模型使用
* `Task Model`中设置  `protected $fillable = ['name', 'created_at']`;
* `datebase/factories/ModelFactory.php`新增
```
$factory->define(App\Task::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->sentence,
        'created_at' => Carbon\Carbon::now(),
    ];
});
```

* `php artisan tinker`
```
namespace App;
factory(App\Task::class,5)->create();
```

[1]:https://raw.githubusercontent.com/upeng/upeng.github.io/master/image/laravel-route-list.png


