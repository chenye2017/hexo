---
title: Laravel 数据填充
date: 2018-03-30 10:01:03
tags: [PHP,Laravel]
categories: Laravel
---

作为一个phper，Laravel的学习是必不可少的，可是这个框架的学习成本还是挺大的，有些东西现在可能不明白，但至少记录下来，慢慢的成长。

<!--more-->

在laravel的学习中，感觉挺好用的相比较于我们现在，数据填充是一个很不错的功能，能生成一大堆假的数据把页面填充起来，让他更有实际的样子。

数据填充，和数据库相关，自然去database下面。seeds目录，databaseseeder,这个文件主要是让我们在运行

```
php artisan db:seed
```

的时候，执行哪些数据填充的文件，其参数可以让我们执行哪些填充文件

```
public function run()
    {
        // $this->call(UsersTableSeeder::class);
        Model::unguard();
        $this->call(UsersTableSeeder::class);
        Model::reguard();
    }
```

上面就是基础的database seeder 里面的内容，前面两个主要是接触限制（官方叫批量赋值，就是默认model里面只有fileable的字段可以填充，为什么要规定这个呢

```
public function create(Request $req)
{
	$this-validate($req, [
  		'name' => 'required|min:5',
  		'password' => 'required|confirmed'
  		···
	])
	$name = $req->name;
	$password = $req->password;
	User::create(compact('name', 'password'));
}
```
这样填充数据太麻烦了，比如我之前在itbasic上面填充数据，一个合同二十多个字段，这样往方法里面插入，一般这样


	public function create(Request $req)
	{
	  $this-validate($req, [
	  'name' => 'required|min:5',
	
	          'password' => 'required|confirmed'
	
	          ···
	  ])
	  User::create($req->all());
	}
req的all方法获取的是一个关联数组，可是这样插入会导致一个问题，比如现在的表的字段的越来越规范了，比如admin字段 is_admin,这样当用户传入is_admin  1， 就完了，所以设置批量赋值，用来规定哪些字段可以传入，哪些字段不可以传入，filable 代表可以传入的，guard 代表这些不能传入。

当我们进行数据填充的时候，可能不需要字段限制，所以才会有了开始的解除限制和后面的开启限制。

接下来就是种子文件。

```
php artisan make:seeder UsersTableSeeder
```

也是只有一个run方法，这个UsersTableSeeder,只是命名规范，其实并不用，因为我们在写run方法的时候，就是在写普通的插入语句


    public function run()
    {
      DB::table('articles')->delete();
      for ($i=0; $i < 10; $i++) {
          \App\Article::create([
              'title'   => 'Title '.$i,
              'body'    => 'Body '.$i,
              'user_id' => 1,
          ]);
      }
    }
这种方法就太没有意义了，因为我们可以通过写一个接口，来进行插入，我们希望的是更真实的数据，laravel中一般使用的用工厂模式生成批量化的对象，然后进行数据填充。

查看database 下面的factory文件夹，


    factory->define(App\User::class, function(Faker faker) {
      static $password;
      return [
          'name' => $faker->name,
          'email' => $faker->unique()->safeEmail,
          'password' => $password ?: $password = bcrypt('secret'),
          'remember_token' => str_random(10),
      ];
    });
这里面只是定一个一个model对象，然后在seeder里面批量化的生成这个对象
    public function run()
    {
        //
        factory(App\User::class)->times(50)->create();
        $user = App\User::find(1);
        $user->name = 'cy';
        $user->email = '1967196626@qq.com';
        $user->password = bcrypt('123456');
        $user->save();
    }
综上：

1. 通过factory ，定义批量化的实例对象。
2. 通过seeder 去实例化生成对象，然后插入数据库中。

