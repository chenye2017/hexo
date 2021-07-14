---
title: PHP yield
date: 2018-05-25 13:55:52
tags:
categories:
---

yield，生成器，自己感觉还是挺重要的，因为php在读取大文件的时候经常会有内存溢出的错误，其实yield还会涉及spl，这部分内容自己目前了解的还不是十分清楚，所以，暂时就把yield的作用归类于放置变量过大在内存中占用本大空间，导致内存溢出吧。

<!--more-->

关于yield这种数据类型，还有迭代器，还有collection，都是能用foreach进行循环遍历的。

```
function productNum($length)
{
 $data = [];
  for ($i=0; $i< $length; $i++){
     $data[] = $i;
	}
 return $data;	
}

$data = productNum($100000000);
foreach ($data as $key=>$value) {
  var_dump($value);
  
}

```

用php执行上端代码会报出超出分配给php的内存错误

```
function productNum($length)
{
 //$data = [];
  for ($i=0; $i< $length; $i++){
     //$data[] = $i;
     yield $i;
	}
 //return $data;	
}

$data = productNum($100000000);
foreach ($data as $key=>$value) {
  var_dump($value);
  
} 
```

能执行成功，其实这个时候打印$data变量，可以看出来他是一个类。



每次foreach 去$data中取数据，上面的那个for循环就会执行一次，再次输出一个变量\$i。

so，如果使用yield的内容，每次也得循环使用吗？



yield定义的内容不能有返回。



下面写一个读取文件，再往另一个文件中写入内容的例子，其实这个例子不具有实际价值，其实就是一个copy文件，用shell_exec执行linux命令就可以，或者file_get_contents,file_put_contents这样就好了，使用yield分多次，唯一可以想到的好处就是控制对内存占用的大小。

```
function getContent($file)
{
  $res = fopen($file);
  while (feof($resource) === false ) {
         yield fgets($resource);
    }
}

$res = fopen('/home/itbasic/3.pdf', 'a');
foreach ($content as $c_key=>$c_value) {
    fwrite($res, $c_value);
}
fclose($res);
echo "使用: ".memory_get_usage()."B\n";  //使用: 362640B
```



用传统方式

```
$res = file_get_contents($file);
file_put_contents('/home/itbasic/1.pdf', $res);
echo "使用: ".memory_get_usage()."B\n"; //使用: 39875608B
```



显然超过一个数量级，yield就是把一个结果集分批的给你，你什么时候问他要下一个结果集，他什么时候给你。



