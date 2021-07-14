---
title: PHP函数
date: 2018-03-26 14:12:29
tags: [PHP,函数]
categories: PHP
---

工作的久了，反而基本功没有原先好了，也许要记得东西很多，内存中不够放置那些无规律的参数位置，但至少不要混淆哪些是框架自带的，哪些是PHP原生的。

<!--more-->

一年多了，重复相同的工作可能让之前很多学会的但没用到的PHP函数忘记了，但这些在modern PHP 中，在新的环境中可能都是很重要的，记录下来。
<!--more-->

1. string

* str_repeat（'cy', 10） //把cy这个字符串重复10次

```
var_dump(str_repeat（'cy', 10）)   //注意一定要打印，这个函数的作用只是构成一个字符串
```
* var_dump 和 var_export
  先来说下以后的使用都是使用var_dump,而不要使用var_export,因为第一个已经很强大了。
  var_dump 可以输出数据类型，但是var_export不可以。
  var_export 第二个参数给true, 他会把这个变量的值存储起来，可以用于赋值，但是当变量是resource的时候，返回的总是null,但是此时var_dump 虽然也不能打印出来，但是能
```
resource(2) of type (stream) 
NULL
$a = var_export([2,3], true);
```
综上，用var_dump

1. 面向对象

is_callable()与method_exists()     //method_exists 只能判断方法是否存在，但如果比如父类的方法是protected这种，没权利执行也会返回true，这种情况就得callable了，他会返回false
```
<?php
class Foo {
    public function PublicMethod(){}
    private function PrivateMethod(){}
    public static function PublicStaticMethod(){}
    private static function PrivateStaticMethod(){}
}
$foo = new Foo();
$callbacks = array(
    array($foo, 'PublicMethod'),
    array($foo, 'PrivateMethod'),
    array($foo, 'PublicStaticMethod'),
    array($foo, 'PrivateStaticMethod'),
    array('Foo', 'PublicMethod'),
    array('Foo', 'PrivateMethod'),
    array('Foo', 'PublicStaticMethod'),
    array('Foo', 'PrivateStaticMethod'),
   );
foreach ($callbacks as $callback){
    var_dump($callback);
    var_dump(method_exists($callback[0], $callback[1]));
    var_dump(is_callable($callback));
    echo str_repeat('-', 10);
    echo '<br />';
}
```

