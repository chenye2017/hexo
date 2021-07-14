---
title: 关于git
date: 2018-09-07 15:51:06
tags: [git]
categories: git
---

公司一直用svn, 怕git 的相关内容以后用到，所以学习了，只是简单记录git 使用过程中自己的问题，不是教程，欢迎一起探讨

<!--more-->

* 首先是分布式和集中式的区别

  分布式就是在每个人的个人电脑上都有仓库，称作本地仓库，本地仓库也会保存整个项目的提交记录，让我们在不连网的情况下也能进行代码的提交，当我们可以连网的时候，再把代码推送到远程仓库，远程仓库只是一个让我们各个工作者可以相互交换提交记录的中转站，我们的提交在git commit 的时候已经完成(我们唯一的提交信息就是在commit 时候写的)，也就是在git log 历史里面已经占据了位置，我们在push 的时候只是用本地的提交记录去覆盖远端的提交记录。而集中式代表着我们在不联网的情况下完全工作提交不了代码，那个中央仓库才是我们的唯一仓库，往里面提交代码才会产生log。所以我们在用集中式的时候，查看log都是通过查看远端log，对比diff也是，所以断网的情况下完全用不了,而分布式完全没有这个问题。

* git add .

  我们每次修改代码之后都需要git add 文件名(. 代表所有文件)添加到暂存区，当我们添加一次之后，又修改了文件，还得git add 一次，因为git add 只是把当前文件现在的修改添加到了暂存区，如果后面修改了，还得重新添加一次，这个可能和svn 的只需要add 这个文件到管理文件夹内，后面修改直接commit 有点不同

* up-to-date

  最新的，有的时候可能显示before one  commit 这种，代表你要给远程分支push啦

* git clone

  做了两件事情 下载了.git 本地仓库，然后把当前目录当做工作目录，把master 最新的提交内容填充当前目录

* .git 

  .git 就是本地仓库，下面的index 代表了暂存区

* git 提交流程

  git add .

  git commit -m

  git pull 冲突发生的地方，pull 只会把需要的内容拉下来，并不能把你之前提交到本地仓库的内容push, 所以pull 之后还是需要push的（感觉这个pull还是挺智能的，在pull的时候如果有冲突，比如修改了同一个文件，还是没有提交到暂存区的那种，需要你先commit 或者废弃，想象一下pull 中包含merge 操作，merge 操作的对象是线上的commit 和 你最新的commit，所以如果有未提交的内容，一定要注意啦，别被覆盖了，但是如果没有关联的文件修改，pull的时候有没有commit 没关系）

  git push

* git comment

  git comment 是实际的提交，会产生log 日志，像我在做itbasic 的时候经常一个功能提交多次，因为有的代码忘记提交了，可以把comment 合并，产生一个log

  git comment --amend ,修正上次的提交，很方便

* git push

  ![image.png](https://upload-images.jianshu.io/upload_images/5525740-01fba848b36b83eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* git branch

  branch 字面上是分支的意思，其实就是一次提交的引用，类似head, master （远端分支head 一定指向master， 可是我们本地head 可以切换到别的分支上呀，而且对应的好像是这个分支的最新提交）

  因为branch 是一个引用，所以我们删掉任何的branch ，都不会删除commit

  自己的分支，因为回退之类的不能修改，那就强行 ， git push origin branch1 -f ，但这种只针对自己吧 （千万别在master 上强行push）

* git checkout

  git checkout -b  feature, 我们新建一个分支并切换到这个分支上面，想把本地分支提交到远端只需要git push origin feature：feature 就好了

  还有就是注意我们在切换分支的时候一定要commit ,想象一下，如果我们没有commit ，在下次切换回来的时候，很多内容是不是就没有了，因为切换到的是这个分支最新的commit 上面 （但是如果是没有冲突的文件变动，是不需要的） 

* git 常用的工作流程

  master fork 出feature 分支，开发完毕merge master 分支，推送到远端feature分支，等待merge 到master 分支上

* git rebase
  主要是用来分支合并的，比如之前在master 分支上开出来的feature 分支，开发完了再合并到master分支上，这时候master分支已经不是原先的master了，可能有向前的提交，就会出现分支的合并，如果不希望出现多条分支合并的情况，可以通过切换到feature分支上，然后git reabase mater, 以新的master分支作为起点，然后git checkout master, git merge feature, 把head 移动到最新的master 开始处

* git reset --hard

  git reset --hard 目标commit 到达任意一个commit ，我觉得直接用sha-1码很方便，小册中说的回退，但是用sha-1码的话，我感觉能到任意一个commit

* git revert

  上面那个感觉是切换到任意一个commit ，如果我们希望重新弄出一个commit，这样提交的时候就不会用冲突（revert 就是把 那个commit 的修改全部撤销）

  感觉上面的两个回退就能满足绝大部分场景了

- 关于分支. git branch -a ，可以查看项目的本地分支和远程分支（分支有本地分支和远程分支这两种，本地分支推送到远程就变成了远程分支，以后别人clone的时候就会包含这个分支）。当我们git clone 远程项目的时候，会把这个项目的远程分支都拉下来，我们可以在本地切换各个分支。各个分支的作用？可以作为开发的时间节点来用，比如慕课网上各个讲课阶段，每次代码开发前，先开个本地分支，开发完了推送到远程，然后和并到主分支上。我们通过切换到各个分支，就知道老师讲到哪了，还能对比代码有哪些变化，以后查看的时候还可以通过编辑器![image.png](https://upload-images.jianshu.io/upload_images/5525740-502973ccb71c0779.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

来进行切换，注意切换的时候会在本地自动新建一个分支，不用担心，你想啊，本地要是不新建，你直接改动的内容算哪的。

* git 和 github 的互动

  git 在我们日常生活中用的最多的就是github，当我们一台崭新的电脑希望从github 中clone 代码的时候（比如git clone xxxx），我们需要ssh 连接到github 上，不做任何处理的情况下会被告知ssh 连接失败，我们需要做的就是在本地电脑上先生成ssh 公钥和私钥（如果没有的话），然后把公钥贴到github 上，公钥的生成过程中需要一个email， 这个其实随便填都是可以的（既然email 是随便填的，但怎么识别这个公钥对应这个用户呢，我觉得应该是这个公钥被贴到对应用户的账号setting处，所以github能识别这个ssh key 对应这个用户，[这里有篇文章写得https 和 ssh 之间的区别](https://debugtalk.com/post/head-first-git-authority-verification/)）。

  我们在仓库的提交过程中也需要配置email 和 用户名，其实这个只是代表着你显示在git log 日志上的用户名和email ，并没有实际的作用

- 今天给easy swoole 提交pr ,详细过程[阮一峰](http://www.ruanyifeng.com/blog/2017/07/pull_request.html)

  首先是从fork代码从easyswoole 到自己仓库中的时候，注意fork的是哪个分支（好像不能把所有的分支都fork下来），然后git clone 到本地仓库，这时候通过编辑器打开，可以把fork 的所有版本都打开，通过

![image.png](https://upload-images.jianshu.io/upload_images/5525740-c79941821a60d76c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

编辑器右下角的切换，可以在本地创建对应分支

```
git branch   本地分支
git branch -a  远程分支
git branch -vv 分支对应情况
```

通过编辑器切换可以自动添加分支关联，如果想手动关联不同分支，查看这篇[文章](https://www.jianshu.com/p/fc433b1686bd)

总结：所以 git push origin master , 这个master代表的就是分支名称喽 （git push origin master:master 其实是这样的，origin 代表的是远端仓库名称，两个master 分别代表本地的和远程分支名称）

