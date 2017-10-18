---
title: 'Laravel Validator自定义参数验证规则'
date: 2017-10-18 23:10:58
tags:
  - laravel
  - validator
categories:
  - PHP
---

## 自定义参数验证规则
`public Validator make(array $data, array $rules, array $messages = [], array $attributes = [])`

**参数说明**
* `$data`是要验证的数据,即 `$request->all()  | $request->input() | Request::all()`
* `$rules`是验证的规则
* `$messages`是提示信息，自定义`rules`中的规则信息
* `$attributes`是设置属性别名

> 使用方法和流程见示例

<!-- more -->

```
use Illuminate\Http\Request;
public function index(Request $request)
{
    $data = $request->input();
    $rules = [
        'id'    => 'required|numeric',
        'age'   => 'required|numeric|max:150',
        'name'  => 'sometimes|max:20',  //sometimes的用意（不传则已，传则必须遵守规则）
    ];
    $messages = [
        'required'  => ':attribute不能为空',
        'numeric'   => ':attribute必须是数字',
        'max'       => ':attribute长度（数值）不应该大于 :max',
    ];
    $attributes = [
        'id'    => 'ID',
        'age'   => '年龄',
        'name'  => '名称',
    ];
    $validator = \Validator::make($data, $rules, $messages, $attributes);
    if ($validator->fails()) {
        return $validator->errors()->all();         //显示所有错误组成的数组
        //return $validator->errors()->first();     //显示第一条错误
    } else {
        return 'validate passed';
    }
}
```
> 上面的messages和attributes都是在使用处自定义设置，有种方法是按照语言在配置文件中定义

1. 修改config/app.php里面的locale为ch
2. 新建 lang/ch/validation.php 文件
3. 修改validation.php文件，可将提示信息修改为中文，attributes是设置属性别名。

另外custom是完全自定义
`
Here you may specify custom validation messages for attributes using the convention "attribute.rule" to name the lines. This makes it quick to specify a specific custom language line for a given attribute rule.
`
```
'custom' => [
    'age' => [
        'required' => '年龄必须存在',
    ],
],
```

