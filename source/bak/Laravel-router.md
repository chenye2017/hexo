---
title: Laravel
date: 2018-07-29 23:41:50
tags: [PHP,Laravel]
categories: PHP
---

关于Laravel 的一些学习

<!--more-->

# Router

```
<?php
Route::get('/', 'StaticPagesController@home')->name('home');
Route::get('/help', 'StaticPagesController@help')->name('help');
Route::get('/about', 'StaticPagesController@about')->name('about');
```

这个name是用来命名的，命名有什么用，看下面

route('help')  => 这个help 是路由命名，user.help 更为准确

route('help',[1,2])  => 多个参数传递数组



这个函数的参数，通过下面这个函数，配合配置得环境变量，可以生成前端可以访问路径的，如果很直白的写url，以后url改动的时候但凡用到的地方，都需要改动

# Input



# Validator

Validator 调用的是facade ， facade 调用是 validator 的factory 类， 其中的make方法生成一个validator 实例，我们可以调用validate 方法去验证，抛出Validation 异常，我们可以通过对validate方法捕获异常，或者判断validator实例执行validate方法之后， 再调用errors()->all() 方法获取错误信息

```
$validator = Validator::make($request->all(), [
   'title' => 'bail|required|string|between:2,32',
   'url' => 'sometimes|url|max:200',
   'picture' => 'nullable|string'
], [
   'title.required' => '标题字段不能为空',
   'title.string' => '标题字段仅支持字符串',
   'title.between' => '标题长度必须介于2-32之间',
   'url.url' => 'URL格式不正确，请输入有效的URL',
   'url.max' => 'URL长度不能超过200',
]);
try {
$validator = $validator->validate(); // 注意不要链式调用，我们需要的是实例对象
} catch (ValidationException $e) {
  
}
// dingo api 对这个异常处理了
// 默认的web 也会有自带的异常处理机制，默认最基础的是绑定信息到模板变量$errors 中， 这个变量内容其实就是 $validator->errors(), 通过 $errors->all() 我们可以获取所有的错误信息
```

为了实现请求验证和控制器的解耦，laravel 把请求单独成requests，直接在controller中传入这个 request 就能实现自动验证，可这样的话，就不知道request 验证什么时候执行的，怎么捕获这个异常，那只能在统一定义异常处理那进行处理 (web 和 api 还不在同一个位置好像，还有怎么在除了控制器的地方直接返回数据给前端，而不接着往下走)



# Exception handle







