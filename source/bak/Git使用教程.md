---
title: git使用教程
date: 2017-12-15 00:14:16
tags: [版本控制工具,git]
categories: 工具
---

感觉有必要写一篇关于git的使用教程，其实像我现在根本用不到git，平时公司用的是svn，然后还是一个人做项目，对于版本控制工具那些处理冲突，回退基本用不到，对于修bug开新的分支，也是没有那个需求，因为一般的bug都比较简单，而且也没那么急。

之所以用git，主要还是因为github吧，上面优秀的代码太多了，（其实是不会写，主要用来下插件）关于git的教程有很多，本身基本的用法也不是很难，写这个文章主要是为了记录我平常的一些基本操作，因为有些东西不用了，就忘记了，然后重新查找，坑爹的Google，每次查找相同的东西总出来不同的结果，坑爹啊，又得重新看一遍，不如自己总结下。

因为自己平时不用git，所以对git很多用法理解的不是很深，只是为了基本的日常写下了这篇文章。

主要参考来源：廖雪峰的git教程 : https://www.liaoxuefeng.com

<!--more-->

先来介绍一下市面上主要的两款版本控制工具svn和git，两者有什么差别呢，svn学名叫集中式版本控制工具，git叫分布式版本控制工具，我对他们之间的区别看待就是：git的操作步骤比svn要长，git在svn后面还有一步操作。svn必须联网才能操作，git没有网也能进行操作，这是一个很重要的点，想象一下，如果你在没网的环境下开发（这个可能概率不大，但感觉作家写作有很大可能）不能用用于提交，当你有网的时候才可以提交的时候，也许你的文章已经写了很多了，这个时候再提交，不管是版本回退，还是这次的修改量都会很巨大，就相当于我们在编程的时候写了一个很大的主函数，以后维护起来会很困难。（刚刚有看到了一点，因为我们公司的服务器就在我旁边，我们在同一个局域网内，所以不管是更新还是下载都会很快，但要是贵阳，云南那些朋友，更新代码，怪不得经常嫌弃慢，有时候还外网访问出问题，这样就更新不了代码了，gg，心疼二帅大周末要来公司修复网络）但是git却可以，git可以让你在没网的情况下进行提交，只是没有提交到远程服务器上，当你有网的时候再推送到服务器上，这很方便，感觉就是没有网的svn，git的冲突处理是出现在把代码远程push到代码仓库的这个联网阶段。
为什么会出现这种情况呢，为什么svn叫集中式，而git叫分布式呢，我的理解就是svn的版本库在远程服务器上，其实git也在远程服务器上，只是他在本机也copy了一个版本库，所以可以理解成每个电脑都是一个版本库，所以有很多的版本库，所以git叫分布式版本控制系统。
所以对于集中式版本控制系统，中央大脑坏了，版本控制工具就不能用，但是分布式中央大脑其实只是用来进行差异化的合并，他坏了，版本控制的功能还是能使用的。这其实是和断网是同一个场景，网断了，不能连接主大脑，svn不能使用，git还是能使用的。
在说git之前推荐款git的可视化工具，小乌龟，不只是git，svn也有，很简单的配置加上点点点，就可以了（以前的我一直把小乌龟当做git和svn··坑爹啊，因为平时一直用小乌龟提交代码嘛，所以就把这个当做版本控制工具了，其实他是版本控制工具之上的可视化管理工具，有svn版，也有git版）
算了，还是先介绍我平时用的比较多的东西吧
1.首先呢，我是因为github才开始使用git的，git的安装直接百度就可以了，下载好git软件（感觉这是git客户端）
2.下载好，然后和所有的windows软件一样，安装好后会生成一个git文件夹，里面有gitbash，这是个很方便的cmd工具哦，相比较window的dos，他支持很多linux的命令，cd啊，ll啊，特别是ssh，window是不支持的，（好像是git是基于ssh的，所以也支持ssh远程连接的啦）
3.然后呢，在github上点击创建个项目，之后下面会出现这个项目地址啊，等等，我一般只是旺这个项目里面塞代码嘛，这个的传输的过程需要ssh的配合，通过命令生成ssh秘钥，把公钥贴在github上，这样就可以支持传输喽（生成的ssh秘钥在该用户.ssh文件下面）
4.执行命令把远程仓库和本地的关联起来，分支什么的，在github上创建好仓库下面会有提示。
5.去到本地项目下面，git init 初始化一个git仓库，然后git add * 把该项目下面所有文件添加进去，然后git commit -m  ""  git的提交好像必须得附加信息，最后push（如果用小乌龟，点点点就可以了，其实也是这个三步，只是小乌龟的add好像得单个文件一一添加，反正第一次添加用git add * ,后面再添加多个文件的数量的时候数量也不会很多，没什么关系）

我大致用的东西大概就这么多，其实在工作的过程中（针对我的svn使用经历，其实开个分支修bug，版本回退还是很重要的，只是因为我平时git用的比较少，所以很容易忘记现在）

下面的内容是看书得来的：
git的好处（或者说是版本控制工具的好处）：感觉两点最能体验：1.协同合作同一个文件的时候，对同一个文件的修改，如何合并多个人员的异同。
2.对于文件修改的历史，特别是基于同一个文件不同的差异较大的修改方向的时候，后来以什么文件为标准，怎么回退到以前的版本，怎么标识每次修改内容。

上述如果通过手动，那将是多么痛苦的过程。

番外：这两天安装composer啊，git都会提到brew，其实这是mac的一个包管理工具，感觉就像yam一样（安装homebrew，然后通过homebrew安装Git，具体方法请参考homebrew的文档：[http://brew.sh/](http://brew.sh/)）

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
这个好像没有什么实际的作用，--global 代表所有的仓库都用这个，还可以给不同的仓库指定不同的name和邮箱。

git init  把一个文件夹变成git仓库，会发现该目录下多了一个.git 文件。

git只能记录纯文本文件的修改（因为文本是有编码格式的，还是用utf-8吧，通用啊）
对于word，图片这种二进制文件，只能记录大小的变化，并不能知道文件哪块修改了，还有不要用记事本（window自带的那个东西写，会出现很多奇怪的东西，我现在系统好像默认的编辑器是subline）

```
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```
小结

现在总结一下今天学的两点内容：
初始化一个Git仓库，使用git init命令。
添加文件到Git仓库，分两步：
第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；
第二步，使用命令git commit，完成。

git status 查看仓库当前状态，不管是没有add的，还是add之后没有commit的（相比较于小乌龟这种，修改之后如果之前add过，后面就不用再add了，但是命令行却还要，廖雪峰说因为add可以一次性加很多文件内容，commit只能一次····，所以才会分成两步） 都能通过git status看见，git diff 发现文章的不同（+代表新加的内容， - 代表减少的内容，一条语句的修改可以看做是先删除这个语句，再加上这个语句）

当工作目录是干净的时候，就代表没有东西被修改了。

git log  查看git 提交的历史

```
$ git log
commit 3628164fb26d48395383f8f31179f24e0882e1e0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Tue Aug 20 15:11:49 2013 +0800

    append GPL

commit ea34578d5496d7dd233c827ed32a8cd576c5ee85
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Tue Aug 20 14:53:12 2013 +0800

    add distributed

commit cb926e7ea50ad11b8f9e909c05226233bf755030
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Mon Aug 19 17:51:55 2013 +0800

    wrote a readme file
```
最近一次提交是 信息是 append GPL
commit后面的id代表的是对这次提交的标记，记得以前用docker的时候对容器的标识也是用这样一大串很长的数字。

git reset --hard HEAD^ 回退一个版本
 git reset --hard 3628164 会退到版本号是3628164···这个版本

版本回退很快，是因为git的版本控制通过的是指针指向的不同。

git reflog 记录每次操作，比如你回退了某个版本，现在后悔了，cmd也关闭了，可以通过git reflog找到版本号，这里面记录了你的每次操作。

```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   readme.txt
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       LICENSE
```
没有加入仓库的文件是untracked,修改了未添加是not stage，都得git add

工作区：我们的工作目录。
暂存区： add文件进来
分支：git init的时候自动创建的，commit的内容都是天价到这个分支上来。

git的每次修改，如果不add到暂存区，commit的时候就不会提交。
可以做一个实验：先修改一个文件->git add->再修改->git commit 查看会发现第二次修改没有提交。

命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：

一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时（git add），想丢弃修改，分两步，第一步用命令`git reset HEAD file`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时(git commit)，想要撤销本次提交，不过前提是没有推送到远程库,git reset --hard HEAD^。

感觉现在学习git，不想想着远程仓库推送的那个环节，那个环节其实就是合并冲突用的，和版本控制无关，git commit之后就会产生版本号。

删除版本库里面的软件：
一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit。（和给版本库添加文件类似）。

在github上创建一个远程仓库，然后本地关联，这个教程太多太多，百度知道上都有，就不写了。

关联远程仓库：
git remote add origin git@github.com:michaelliao/learngit.git （orgin是远程仓库的名称）

git push -u origin master  关联本地master分支和远程master分支（之后的推送就只要git push origin master）

我平时都是使用git的流程都是现在本地剑豪文件夹，然后推送到远程。

还有种是在从远程clone文件，创建仓库的时候勾选初始化，会新建一个md文件，然后git clone

git支持多种协议，除了ssh，还有https，但ssh更快，除非一些公司不开放ssh端口，那只有用ssh了（注意前面名称还是git）























































































