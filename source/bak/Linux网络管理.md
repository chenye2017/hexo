---
title: Linux网络管理
date: 2017-11-28 01:17:16
tags: [网络,Linux]
categories: Linux
---

首先来介绍下有关网络的相关知识吧，iso，国际标准化组织  制定了 osi七层模型，但这个模型在现实生活中并没有使用，使用的是基于7层模型之上的tcp/ip 4层模型

![OSI七层模型](http://ozys8fka7.bkt.clouddn.com/Image2.png)
<!--more-->

![tcp/ip四层模型](http://ozys8fka7.bkt.clouddn.com/Image.png)
![二者对应关系](http://ozys8fka7.bkt.clouddn.com/Image1.png)

二者区别
![](http://ozys8fka7.bkt.clouddn.com/Image6.png)

上三层是给用户提供服务的，下四层是数据传输用的（数据不传输的下4层不用了，但上3层还是要用的）

既然是数据传输，那必然就有单位，每一层传输的单位是不一样的，osi7层模型上都有标识，最底层的物理层单位比特，代表的就是0或者1这个单位
这些层在水平上的传输，然我们以为他们就是直接想通的，其实他们是从高到低，再从低到高这样传输的。

各个层的作用，感觉了解一些就可以了
1.物理层：底层传输
2.数据链路层：通过不同的mac地址，通过交换机进行传输，此时还没有ip地址，so交换机肯定是不认识ip地址的啦
3.网络层：ip地址，路由器，通过对不同路由器的选择，去寻找不同的主机服务器
4.传输层：tcp udp的定义，tcp安全可靠没有udp快，但udp不可靠，还有来确定端口号（端口号的理解，这封信送给你家，只有署名了，才知道给你家的某个人，外来的请求，只有确定了端口号，你才知道这个请求是给哪个服务的）
5.会话层：这个文件是直接存储呢，还是要进行网络传输呢
6.表示层：数据的表现形式，加密啊，我们window上文件的高级属性也能加密，但是秘钥保存在本机应该是c盘，一旦重装系统，秘钥丢失，那这个文件也可能就打不开了
7.应用层：用户接口

线面这张图能表名上面的作用
![](http://ozys8fka7.bkt.clouddn.com/Image5.png)

![tcp的三次握手](http://ozys8fka7.bkt.clouddn.com/Image3.png)
a: 你在吗？
b:我在，你还在吗？
a：我还在，我穿输了
![两军问题](http://ozys8fka7.bkt.clouddn.com/Image4.png)

其实这个应答应该是没完没了的，但3次之后准确率就比较高了

下面介绍一下ip有关内容
![](http://ozys8fka7.bkt.clouddn.com/Image7.png)
ipv4结构如上所示，图中可以看出选项有的有，有的没有，所以结构不唯一，需要检测，没有ipv6固定，速度快

可以看出32次方，一共2的32次方个
默认 0.0.0.0 到255.255.255.255
其实如下，很多不给用的，就a b c类能用，其中还包括一些私有的，只有局域网内网才能用
![](http://ozys8fka7.bkt.clouddn.com/Image8.png)
127这个网段只有一个也就是自己 
127.0.0.1

上面的ip地址第一个字段代表不同的网段，不同的网段需要通信，要用路由器

 

a类网段，拥有的主机数是 2的24次方
b类：前两个数代表不同网段，后面两个数代表不同的主机
c类：前三个数代表不同网段，后面1个数代表不同的主机
同一个网段交换只要交换机就可以了
这个网段是怎么决定的呢，是有子网掩码决定的，子网掩码的255代表网段，0代表主机，ip地址都是配合子网掩码使用的，没写是因为有默认

最大的主机数（-2 一个是网络地址，一个是广播地址）

私有ip不要钱，有效的保护公网ip不够用

缺点：不能访问公网ip，公网ip也不能访问私网ip（公网ip是互联网上唯一的门牌号）

![](http://ozys8fka7.bkt.clouddn.com/Image9.png)

网络的计算：ip地址和子网掩码和，因为子网掩码前面全是1，所以网络地址网段和ip地址一样，后面子网掩码全是0，所以网络地址主机是0

广播地址怎么算呢，子网掩码有多少位是0，就换算多少位1，前面网段不变，这样就是广播地址

![tcp](http://ozys8fka7.bkt.clouddn.com/Image10.png)
![udp](http://ozys8fka7.bkt.clouddn.com/Image11.png)
udp 比tcp简单，所以udp比tcp块

常见端口号
http 80 https 443
mysql 3306
ssh 2222
redis 忘记了
smtp 25(简单邮件传输协议)

不管是window还是linux都禁止了23端口，因为telnet是明文传输，截获了都不用破解

DNS 进行域名解析

 虽然我们的端口分tcp和udp，但是系统怕我们弄混淆了，不管tcp的20 21还是udp的20 21都是分配给ftp使用的

DNS（尽然既可以接受tcp协议也可以接受udp协议）

![nestat -an](http://ozys8fka7.bkt.clouddn.com/Image12.png)
listening 表示本机正在监听

establish 表示建立连接

udp的状态为空，因为udp不管你在不在，都会给你发送数据

（我想攻击一个游戏服务器，我把我所有的外部连接都关掉，然后登陆游戏，然后netstat 查看外部连接，就能知道对方的ip地址了）

关于DNS的知识

ip地址太难记，没有域名形象，so 产生了DNS

window的host文件是做静态ip和域名对应，优先于DNS匹配，so我们经常本地测试绑定虚拟域名的时候都是这么干的

早期就是通过host文件这么解析的，坏处
![](http://ozys8fka7.bkt.clouddn.com/Image13.png)

dns原理
![](http://ozys8fka7.bkt.clouddn.com/Image14.png)

![](http://ozys8fka7.bkt.clouddn.com/Image15.png)

域名解析原来是从后往前的，··顶级域名在后面

![](http://ozys8fka7.bkt.clouddn.com/Image16.png)
![](http://ozys8fka7.bkt.clouddn.com/Image17.png)
原先有个.me 的国家域名可以申请

这个是全球唯一的（比如www.sina.com 和www.sina.cn就是两个域名，为了防止别人误入错误的地址，大公司会把那些顶级域名都注册了，以免坏人的误导）

三级+二级+顶级+组成完整的域名

根域名管理一级域名，一级域名管理二级域名，二级域名管理三级域名，这种层层管理

dns一般劫持被误导非常难，你只要确保imooc.com 这个二级域名是否是这个网站的，就能确保是否是钓鱼网站（确保二级域和顶级域一致）

域名解析过程
主要分为开始的递归查询和后面的迭代查询
![](http://ozys8fka7.bkt.clouddn.com/Image20.png)
默认本地域名服务器解析的域名保留3天

（这种分级的更有利管理）

![](http://ozys8fka7.bkt.clouddn.com/Image21.png
)
迭代查询允许返回一个最优的值，比如顶级域名不知道，让本地域名服务器去找cn解析

全球所有的域名服务器都知道13台根域名服务器

但是递归查询不可以，递归要么返回一个准确值，要么返回错误

所以，递归查询一般用作客户机和本地域名服务器之间进行查找（注意这个查找虽然图中只是一台，其实会有很多台）
而迭代查询一般用在根dns和cn，com这些之间进行查找



