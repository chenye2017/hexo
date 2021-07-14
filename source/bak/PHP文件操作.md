---
title: PHP文件操作
date: 2018-01-29 16:04:01
tags: [PHP,文件操作]
categories: PHP
---

上篇文章里面写了关于文件上传。其实在做文件上传的功能的时候遇到很多php关于文件的处理，记录下来。其实文件处理在我们刚开始学习php的时候肯定学习过，可后来因为项目当中，做网站的时候关于文件的操作很少，所以很容易忘记这部分的内容，毕竟其实也挺枯燥的。
<!--more-->
1.遍历文件夹下的所有文件。
在做大文件切割的时候，本来想用php做，可是php的fopen好像最多只能接受8000多个字节，就是8000B，而我们上传文件大小一般能达到4m,如果按照8000B的切割也就太多小文件了，于是用linux新建个文件夹，在文件夹中split -b  4m 文件名 （还有个参数代表切割文件的名称，我没有仔细看，使用的默认的，就会产生xaa,xab···这种的文件），然后删除原始文件，遍历文件夹下的所有文件，每次上传一个，像服务器上刚开始创建好的文件持续写入，这样就能实现php的大文件上传
php:
```
<?php
$dir = '/root/test';
$handle = opendir($dir);
$fileArr = [];
while($filename = readdir($handle)) {
     if ($filename != '.' && $filename != '..' && !is_dir($filename)) {
            array_push($fileArr, $filename);
        }

}
//var_dump($fileArr);exit;
        
```
2.遍历文件夹下所有文件
这个是面试题中经常考得一道题

php
```
function listDir($dir, &$a)
{
	if(is_dir($dir))
   	{
     	if ($dh = opendir($dir)) 
		{
        	while (($file = readdir($dh)) !== false)
			{
     			if((is_dir($dir."/".$file)) && $file!="." && $file!="..")
				{
     				echo "<b><font color='red'>文件名：</font></b>",$file,"<br><hr>";
     				listDir($dir."/".$file."/", $a);
     			}
				else
				{
         			if($file!="." && $file!="..")
					{
						$a[] = $file;
         				echo $file."<br>";
      				}
     			}
        	}
        	closedir($dh);
     	}
   	}
}
//开始运行
$a = [];
listDir("./nowamagic", $a);
```
上面两个场景大致差不多，主要就是几个函数的使用。首先要明确读取文件夹的内容和读取文件的内容是不一样的。
1. is_dir 判断该文件是否是文件夹，注意的一点事一定要跟上文件名的前缀··，否则不能找到指定文件，会有奇怪的错误发生。

2. is_file 判断是否是文件

3. 读取文件夹的内容，首先要打开文件夹,opendir() ,会产生一个资源型的数据类型，句柄，readdir() 会循环遍历文件夹的内容。closedir()读完文件之后关闭。

4. 注意每个文件夹下的两个特殊文件'.' 和 '..'

5. fopen打开一个文件，返回一个句柄，其实和上面打开一个文件夹是一样的，只是这里面多了几种模式。这个句柄可以理解成连接数据库时候的资源文件，每次关于数据库的操作都得用到这个文件

   ```
   "r" （只读方式打开，将文件指针指向文件头）
   "r+" （读写方式打开，将文件指针指向文件头）
   "w" （写入方式打开，清除文件内容，如果文件不存在则尝试创建之）
   "w+" （读写方式打开，清除文件内容，如果文件不存在则尝试创建之）
   "a" （写入方式打开，将文件指针指向文件末尾进行写入，如果文件不存在则尝试创建之）
   "a+" （读写方式打开，通过将文件指针指向文件末尾进行写入来保存文件内容）
   "x" （创建一个新的文件并以写入方式打开，如果文件已存在则返回 FALSE 和一个错误）
   "x+" （创建一个新的文件并以读写方式打开，如果文件已存在则返回 FALSE 和一个错误）
   ```

   这个模式也可以分成指针指在文件头部还是尾部来记忆。

6. feof 判断指针是否到了文件尾部 (feof($handle), 判断光标在不在末尾) 

7. fgets读取一行文件，也会移动光标到下一行（可以通过fseek 重置光标位置）

8. fgetc 读取一个字符，(注意比如 \n 属于一个字符)

9. 怎么理解 $handle, 连接资源。感觉可以理解成一个光标，比如我们 a+ 打开，光标在末尾，所以我们写入文件也在末尾

10. fseek(\$handle, \$position, SEEK_END) 我们把光标移动到末尾，然后可以倒着读取单个字符，遇到 PHP_EOL 也就是一行

11. fclose关闭文件 (怎么理解关闭文件，想象成一个tcp连接，我们如果不及时关闭这些连接，会造成资源浪费，而且如果有锁的话，还可能让别的写入进程等待，但是我们连接要是释放了，资源也就释放了)

12. fgetcsv 与 [fgets()](http://www.w3school.com.cn/php/func_filesystem_fgets.asp) 类似，不同的是 fgetcsv() 解析读入的行并找出 CSV 格式的字段，然后返回一个包含这些字段的数组。

13. fread(), 读取指定大小的文件，但有大小限制，然后还有二进制文件的限制（没有仔细了解过二进制文件和文本文件的区别）

14. 上面代码还需要注意的一个地方是对于&的使用，我们只需要在函数定义的时候给上就好了，并不用在函数的使用的是时候也加上，这个比较static的优点就是在多个文件夹都需要遍历的时候的好处，static 会把一次程序的执行所有的这个变量的值都叠加，但是取地址符每次修改的时候都是这个变量的值，举个例子吧，网上对于无限递归的处理

    ```
    $dept = [
        [
            'id' => 1,
            'name' => '爷爷',
            'parent' => 0
        ],
        [
            'id' => 2,
            'name' => '爸爸',
            'parent' => 1
        ],
        [
            'id' => 3,
            'name' => '姑妈',
            'parent' => 1
        ],
        [
            'id' => 4,
            'name' => '表哥',
            'parent' => 3
        ],
        [
            'id' => 5,
            'name' => '我',
            'parent' => 2
        ]
    ];
    ```


    function getParent($allparent,$we) {
        static $p = [];
       /*  if ($we['parent'] == 0) {
            array_push($p, []);
            
        } else { */
            foreach($allparent as $key=>$value) {
                if ($we['parent'] == $value['id']) {
                    array_push($p, $value);
                    getParent($allparent, $value);
                }
            }
        /* } */
        return $p;
    }
    
    $res = getParent($dept,  [
        'id' => 5,
        'name' => '我',
        'parent' => 2
    ]);
    $res1 = getParent($dept,  [
        'id' => 4,
        'name' => '表哥',
        'parent' => 3
    ]);
    var_dump($res1); 
    
    // 改进
    function trueParent($allparent, $we, &$p)
    {
        foreach($allparent as $key=>$value) {
            if ($we['parent'] == $value['id']) {
                array_push($p, $value);
                trueParent($allparent, $value, $p);
            }
        }
        return $p;
    }
    
    $res3 = [];
    trueParent($dept,  [
        'id' => 5,
        'name' => '我',
        'parent' => 2
    ], $res3);
    $res4 = [];
    trueParent($dept,  [
        'id' => 4,
        'name' => '表哥',
        'parent' => 3
    ], $res4 );
    
    var_dump($res3, $res4);
    exit;
    ​```
    
    运行下上面的程序，你会发现$res是正确的，但是$res1其实也能获取到，只是res1会附带上res的结果，根本原因是static 变量的问题，而且static还不能释放掉，unset没有效果，但这种情况还是挺常见的，比如当我既要获取我的祖先还有表哥的祖先，当我们循环获取的时候，这时候&地址符就能解决我们的问题

3.php合并文件
思路：其实就是打开一个文件，不断往里面写入。关于打开fopen() 参数很多，感觉很多都是遇到的时候再看吧，二进制文件记得加b。
php
```
$fb = fopen('/root/1.css', 'wb');
foreach ($fileArr as $key=>$value) {
    fwrite($fb, file_get_contents('/root/test/'.$value));
}
fclose($fopen);   
```
每次的写入都是从尾部开始写入的。（相比较打开文件夹，他少了个readdir这种循环遍历文件夹的函数，好像fgets也有这种功能，但是用的不多，就没细看了）。

4.还有一些关于文件的函数，比如获取文件内容file_get_contents,写入file_put_contents,获取fgets这种的好像是按照行获取的，一般读操作用file_get_contents,能读取全部的内容。

> 文件读写是经常进行的一个动作，读取文件的函数真是千千万万个，复杂的有，简单的也有。最常用方便的有file_get_contents(),file_put_conents()，不需要进行打开文件，关闭文件的操作。 
> 但是对超大文件进行读取时，file_get_contents()会把内容都读取进内存，造成内存溢出，最好是循环按行读取。fgetcsv()用来读取一行csv文件，fgets()用来读取一样普通文件。