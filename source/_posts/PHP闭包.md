---
title: PHP闭包
date: 2018-03-26 22:10:03
tags: [PHP,匿名函数,闭包]
categories: PHP
---

咱是写PHP的，可是PHP的很多特性咱都不是很熟，比如trait，闭包，咱不能只局限于表单的操作，不能只局限于面向过程的写法，记录下来那些年很少接触的特性。就从PHP的 **闭包** 开始咯
<!--more-->

>1).闭包和匿名函数在PHP5.3中被引入。
>2).闭包是指在创建时封装函数周围状态的函数，即使闭包所在的环境不存在了，闭包封装的状态依然存在，这一点和Javascript的闭包特性很相似。
>3).匿名函数就是没有名称的函数，匿名函数可以赋值给变量，还可以像其他任何PHP对象一样传递。可以将匿名函数和闭包视作相同的概念。
>4).需要注意的是闭包使用的语法和普通函数相同，但是他其实是伪装成函数的对象，是Closure类的实例。闭包和字符串或整数一样，是一等值类型。

第一次接触闭包还是在js中，后来是在python中，最后才是php中！！!深刻的怀疑我是不是做php的。

js 中闭包

```
function out() {
        var a = 1;
        var b = function(name) {
            console.log(a);
            console.log(name)
        }
        return b;
    }

    var a = 2;
    var name = 'cy';
    var c= out();
    c(name);  // 输出 1， cy
```

上面这段代码虽然简单，但是包含了几个重要的知识点：

js的闭包主要是靠两层函数的嵌套，然后通过调用外层的函数返回内层的函数，内层的函数可以调用外层函数中的变量，内层函数的执行可以通过后来调用的时候传入参数。



php 中匿名函数和闭包是同一个意思，所以下面过程中说到php匿名函数可以自动脑补成闭包，或者说到php闭包可以自动替换成匿名函数。

我觉得之所以php中匿名函数等同于闭包，是因为php中匿名函数完全符合闭包的定义，php中的匿名函数默认都是预定义接口closure 的一个实例，闭包本身是保存运行状态，他默认保存的是对象是预定义closure, 然后其他变量通过use 引入!!!这点很关键，看下面php 和 js 的代码，来看看php匿名(闭包)和js匿名函数(非闭包)，体会下区别

```
php
<?php
$a = 1;
$b = function () use ($a) {
  	var_dump($a);
};
$a = 2;
$b();  // 1

js
var a = 1;
var b = function () {
  console.log(a);
};
var a = 2;
b();  // 2
```

use 关键字让php匿名函数保存了当时变量状态，但是js的匿名函数却不行

相较于js 的闭包对象不可控，总是变（es6中有语法可以绑定）,php中的bind和bindto 方法是真的很方便，可以把匿名函数绑定到任何对象上，bind 属于 closure的静态方法（匿名函数, 对象，类名）， bindto（对象，类名）不是。

```
php.net
Closure::bindTo — 复制当前闭包对象，绑定指定的$this对象和类作用域。
Closure::bind — 复制一个闭包，绑定指定的$this对象和类作用域。
需要注意的是，我们绑定之后会返回一个新的closure，新的closure才是绑定对象的
```

（之前不知道为啥要绑定类名，为的是让匿名函数中可以用$obj 类似在绑定类中使用，要不然只绑定对象的话，是不能调用protected 和 private 方法的，蛮好理解的）

(如果匿名函数在类中定义，匿名函数中的$this 指代的就是这个类对象， 要是在类外面定义指代的就是closure对象，要是在匿名函数没有绑定对象（定义在类外面并且没有用bind绑定对象），匿名函数中就不能直接使用 \$this 了)

(虽然php中匿名函数可以绑定对象，但是就算绑定了新对象，他还是closure 的实例，只是有些属性改变了，如果不相信，可以直接打印这个实例, 正因为还是closure 实例，我们仍然可以给这个闭包绑定新的对象)



说了这么多，看一下实际代码中的闭包

```
class Foo
{
    public function __call($method, $args)
    {
       var_dump(111);
        // else throw exception
    }
	
}

$obj = new Foo('Sam');
$obj->say = function () {
    return 'Hello World';
};
echo($obj->say());  // 111  ，触发 Foo __call
echo(($obj->say)());  // Hello  World, 触发 closure __invoke
```
分析一下执行过程：当执行的say() 方法不存在的时候会自动执行 $obj所在类 的__call魔术方法，然后method 传入是字符串'say'，这里面args 因为是空(say())，所以自动传入为空。输出 111

当执行(\$obj->say)(), 并没有触发\$obj 所在类的方法，而是触发closure类的方法



php中匿名函数保存的变量能直接执行都是因为一个魔术方法\_\_invoke, 这个魔术方法是在把类实例当做方法调用的时候会自动触发，因为php的匿名函数本质上都是closure类的实例（这个closure类中有四个方法 \_\_invoke __construct   static bind   bindTo）,所以当我们 var(), 这样的时候，自动触发closure 的 \_\_invoke，(猜测这个\_\_invoke 的作用就执行后面的匿名函数)所以匿名函数就被执行了。



```
class Foo
{
    private $name;

    function __construct($name)
    {
        $this->name = $name;
    }

}

$obj = new Foo('Sam');

$cl = function() {
    return "Hello " . $this->name;
};

$cl = $cl->bindTo($obj, $obj); //第二个$obj 感觉类名好点，这个实例的话也是通过反射获取类名的吧
echo($cl());
```
下面这段台湾大佬的话感觉就是说明了一个意思，bindto的第二个参数或者bind的第三个参数是让代码的执行类似在绑定的类中,第二个参数就是扩大第一个参数的类的使用权限问题。但仅仅在于扩大原始的不能访问的protected和private，并不扩大注入对象没有关系的或者说注入对象所在类不能访问到的变量。

>我們不再執著該closure一定要動態成為 $obj的method，但要存取$obj property的目標不變，程式也不變，一樣使用$this。
>假如我們能將$obj以手動注入的方式，讓closure內部的$this改指向$obj，我們就能達到如JavaScript的效果了。
>$cl = $cl->bindTo($obj, $obj);
>bindTo()如同__invoke()一樣，是closure物件內建的method，它的目的就是讓我們能手動注入一個物件，讓closure物件的$this指向手動注入的物件$obj。
>因為在closure中我們有$this->name，經過bindTo()去手動注入 $obj後，$this已經改指向$obj，所以$this->name就相當於$obj->name。
>根據bindTo()文件 :
>若要讓closure物件只能存取其他物件的public變數，只傳第1個參數即可。
>若要讓closure物件存取其他物件的private或protected變數，就要傳第2個參數。
>bindTo()對於第2個參數的要求不嚴，有幾種傳法 :
>傳進欲存取物件的class名稱，是字串。
>傳進欲存取的物件也可以，bindTo()會自動得知該物件的class名稱。
>在此就一併傳進與第一個參數相同的$obj。
>echo($cl());
>因為$obj已經透過bindTo() 手動注入進$cl()，此時$this已經指向$obj，所以執行$cl()就可順利存$obj的property



bindto 实例代码

接下来我们来看看bindTo方法，通过该方法，我们可以把闭包的内部状态绑定到其他对象上。这里bindTo方法的第二个参数显得尤为重要，其作用是指定绑定闭包的那个对象所属的PHP类，这样，闭包就可以在其他地方访问绑定闭包的对象中受保护和私有的成员变量。
你会发现，PHP框架经常使用bindTo方法把路由URL映射到匿名回调函数上，框架会把匿名回调函数绑定到应用对象上，这样在匿名函数中就可以使用$this关键字引用重要的应用对象：
class App {
    protected $routes = [];
    protected $responseStatus = '200 OK';
    protected $responseContentType = 'text/html';
    protected $responseBody = 'Hello World';
    
    public function addRoute($path, $callback) {
        $this->routes[$path] = $callback->bindTo($this, __CLASS__);
    }
    
    public function dispatch($path) {
        foreach ($this->routes as $routePath => $callback) {
            if( $routePath === $path) {
                $callback();
            }
        }
        header('HTTP/1.1 ' . $this->responseStatus);
        header('Content-Type: ' . $this->responseContentType);
        header('Content-Length: ' . mb_strlen($this->responseBody));
        echo $this->responseBody;
    }

}
这里我们需要重点关注addRoute方法，这个方法的参数分别是一个路由路径和一个路由回调，dispatch方法的参数是当前HTTP请求的路径，它会调用匹配的路由回调。第9行是重点所在，我们将路由回调绑定到了当前的App实例上。这么做能够在回调函数中处理App实例的状态：
```
$app = new App();
$app->addRoute(‘/user’, function(){
    $this->responseContentType = ‘application/json;charset=utf8’;
    $this->responseBody = '世界你好';
});
$app->dispatch('/user')
```





Laravel
中对闭包的使用
IoC 容器
匿名函数可以从父作用域继承变量，而这个父作用域是定义该闭包的函数（不一定是调用它的函数）。
利用这个特性，我们可以实现一个简单的控制反转IoC容器：
```
class Container
{
    protected static $bindings;
 
    public static function bind($abstract, Closure $concrete)
    {
        static::$bindings[$abstract] = $concrete; 
        //如果还需要container自身的一些方法的话
        //可以这么写： static::$bindings[$abstract] = $concrete->bindto($this, $this);
    }
 
    public static function make($abstract)
    {
        return call_user_func(static::$bindings[$abstract]);
    }
}
 
class talk
{
    public function greet($target)
    {
        echo 'Hello ' . $target->getName();
    }
}

class A
{
    public function getName()
    {
        return 'World';
    }
}
 
// 创建一个talk类的实例
$talk = new talk();
 
// 将A类绑定至容器，命名为foo
Container::bind('foo', function() {
    return new A;
});
 
// 通过容器取出实例
$talk->greet(Container::make('foo')); // Hello World
```
上述例子中，其实并不是很能说明bindTo的特性，为什么呢，分析一下执行流程（分析这个例子主要是因为当时不太懂闭包的时候网上找的）：

首先是把一个匿名函数绑定到一个类的属性中，然后获取这个属性的时候直接调用匿名函数，因为上面的匿名函数就是获取一个类的实例，根本用不到 this 相关的东西，所以和给这个closure 绑定对象毫无关系

上面的写法其实转换一下就和下面一样 

```
<?php

class A {
    public function getName() {
        return 'cy';
    }
}

$app = [];

$app['foo'] = function () {
    return new A();
};


class Talk{
    public function talk1 ($obj) {
        var_dump('hello '. $obj->getName());
    }
}

$t = new Talk();

var_dump($t->talk1(($app['foo'])()));

exit;

```



今天说一下对于闭包的重新认识：

首先是array_map里面的内容。array_map 用到了闭包的知识，函数作用：通过对传入的数组进行自定义的函数处理，传入的数组多少个，自定义的函数接受的参数就要多少个

```
<?php
array_map(function($i, $z) {
  var_dump($i+$z);
}, [1,2,3], [2,3,4]);
```

但如果我们想传入额外的参数呢

```
<?php
$z = 100;
array_map(function($i, $z) use ($z) {
  var_dump($i+$z);
}, [1,2,3], [2,3,4]);
```

通过use

这和我之前看到的内容都是一致的，只是这次我希望不要把这个use和bindto绑定的参数作用弄混淆了，bindto的目的是修改closure类里面this对象.

对了php的闭包很类似于js的万物皆是对象的概念，function也是对象

对的php中的匿名函数，也就是闭包，他也是对象，他是closure类的实例

但和js不同之处，php的对象不能改变，所以匿名函数中的this指向的是closure这个实例（但是可以改变比包中this 的指向），这个实例除了——invoke和 bind（静态方法）bindto() 一无所有

但是我们可以通过bind，我的理解是让两个实例互相拥有对方的属性，其实不是

```

class Foo
{
    private $name;

    function __construct($name)
    {
        $this->name = $name;
    }

}

$obj = new Foo('Sam');

$cl = function() {
   var_dump($this instanceof Closure); //false
   var_dump($this instanceof Foo); //true
    return "Hello " . $this->name;
};

$cl = $cl->bindTo($obj, $obj);
echo($cl());
```

zzz,感觉绑定之后这个closure的实例就有了家的感觉，就变成了那个被绑定的类了（明明打印出来还是closure 类，但却是绑定的类的实例）。



额外说一点：

我们在使用闭包的时候，function(){}，这种只是定义了一个变量，并没有使用，我们想使用的话，比如function() {} (),这样，或者是 function(){} ->\_\_invoke();

这个可以理解成 function(){}  是一个对象，当我们调用他的时候就自动触发了Closure类里面的\__invoke方法，所以上述是等价的结果。(但感觉不要这样用吧，很少调用魔术方法)

正因为匿名函数定义的时候没有调用，总是在需要的时候调用，所以容易造成异步的假象

关于匿名函数的应用：

匿名函数在laravel 中或者很多现代框架中大量使用，我们千万要注意，

我们在写匿名函数的时候，只是在定义这个函数，实际调用是框架自身决定的，所以我们在匿名函数function() 中传入的参数是由框架执行的时候决定的，我们可以不传，没关系，因为php 可以在函数定义的时候没有参数，执行的时候有参数也不会报错，但是我们要是传了参数，就会和框架中执行的时候传入的参数对应上，也就是预定义参数，一般情况框架使用文档上都会有，拿laravel路由举例

（特别注意，但是我们不能执行的时候没有穿, 定义的时候却有，除非是可变参数，否则都不可以）

```
// 下面4种定义匿名函数都没有关系
// 对于传入额外参数我是这样猜测的，获取非匿名函数定义的参数，和预定义参数合并，然后去执行
$route->get('/{id}', function($id) {
  var_dump($id);
});
$route->get('/{id}', function() {
 
});
$route->get('/{id}', 'TestController@index')

class TestController {
  public function index(class1 $a, class2 $b, $id) {
  
}  

或者 
 public function index(class1 $a, class2 $b) {
  
}  
或者 
 public function index(class1 $a, class2 $b, $name) {
    // 这里面的$name 等值于 $id
}  
  
}

```





