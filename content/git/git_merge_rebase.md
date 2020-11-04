---
author: "li_mingxie"
title: "git命令merge和rebase的简单应用"
date: 2018-07-29T07:28:49+08:00
tags: [
    "git",
    "merge",
    "rebase",
]
categories: [
    "git"
]
---

因为工作的需求，项目分了几个大分支管理了。  
不同的环境需要的功能也不一样，在不同的分支里修改。  
虽然我不太喜欢这种做法。但是借此机会对merge和rebase, cherry-pick命令研究了一些。<!--more-->
(我比较倾向，有一个主分支，发布在不同的环境是应该由不同的配置和适当的继承来实现。  
如果这么做实在太麻烦的话，应该另分出一个仓库repository管理。)

以下是我简单整理的内容。

### **◆ merge**  
merge就是合并不同分支的代码。  
先举个简单的使用场景例子。    
从master分支分出一个dev分支开发。  
期间我们master分支有 m1,m2,m3 的提交记录。  
dev分支有 d1,d2 的提交记录。  
2个分支需要合并代码。

```
$ git merge dev     --在master分支把dev分支的代码合并过来。
```  
把2支分支合并，`有冲突，解决冲突后需要提交一次合并代码记录`。  
(为了简单，下图是没有冲突时的场景。)

[图片备用地址](https://limingxie.github.io/images/git/git1.png)  
![1](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git/git1.png)

### **◆ rebase**  
rebase其实就是重置代码历史，手动的修改提交记录和代码。  
一般项目当中`有两种用法`。  

**1. 合并记录**  
这是一般开发者处理完Issue后，给主分支发出合并请求了。  
但是CodeReview的时候发现有不合适的代码，或是有个小Bug需要修改。  
修改后又出现一个提交记录，1个Issue有2个提交记录，看着不舒服。  
这时候用以下方式处理。  

```
$ git rebase -i HEAD~2      --2代表要修改最近2个记录
```

[图片备用地址](https://limingxie.github.io/images/git/git2.png)  
![2](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git/git2.png)

**2. 调整顺序**  

`git merge`语法是按照时间顺序合并代码的。  
`图1`所示`merge是时间的顺序`来合并代码的。  
```
master: m1 -> m2 -> m3
dev: d1 -> d2
==>    -- 合并的代码记录是按照时间的排序
master: m1 -> d1 -> m2 -> d2 -> m3 
```
当开发到 m2 的时候我们发布了一个版本，合并了d1,d2后发布出现问题了。  
我们现在急需回滚，到 m2 的版本。  
 怎么回滚？ 回滚到m2吗？ m1吗？ 这提交记录怎么这么乱 .. .. ㅠㅠ  

我们希望看到的提交记录是 
```
master: m1 -> m2 -> m3 -> d1 -> d2
```

这时候我们不能用merge，而是要用rebaes合并代码。 

```
$ git rebase master     --在dev分支先把master分支的记录拉倒自己的底下...
```

[图片备用地址](https://limingxie.github.io/images/git/git3.png)  
![3](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git/git3.png)

### **◆ 分支branch管理策略** 
那我们如运用merge和rebase管理分支呢？  
一般我们是以工单Issue的单位做开发。  


    1. 先从远程仓库拉取最新代码。 git pull upstream master  
    2. 创建Issue分支。 git checkout -b Issue_1  
    3. 开发完成后，向远程仓库发出合并请求(+ CodeReview)。 Merge Requests Issue_1  

如图4所示。 

[图片备用地址](https://limingxie.github.io/images/git/git4.png)  
![4](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git/git4.png)

**解决冲突**  

好几个人一起开发冲突是难免的， 那发生冲突如何解决呢？  

举例子：  
你接到了Issue_2的事项。  
这时远程仓库的代码是合并了Issue_1的事项。  
你在本地库(master-local)里先把最新的代码拉过来。  
创建Issue_2的分支进行开发。  

[图片备用地址](https://limingxie.github.io/images/git/git5_1.png)  
![51](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git/git5_1.png)

你开发完成以后，发合并请求的时候发现:  
有个“缺德”的开发者已经处理好Issue_3的事项，而且已经合并到主分支。  
怎么办？  

1. 本地master分支从新拉取最新代码。  
2. 然后切换到Issue_2的分支。  
3. git rebase master。 
4. push Issue_2分支。(如果之前提交过一次，需要用--force 强推) 
5. 发合并请求。(如果之前发过，做到push分支代码的步骤就可以，不用再发合并请求。) 

[图片备用地址](https://limingxie.github.io/images/git/git5_2.png)  
![52](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git/git5_2.png)

从新整理一下命令的顺序...^^  
当发生冲突的时候：

    1. git checkout master
    2. git pull upstream master
    3. git checkout Issue_2
    4. git rebase master
    5. git push origin Issue_2:Issue_2 --force   //因为rebase是修改历史记录，有提交过的话需要强推。

还是不明白的话可以细细的再看图5.2。  
还是不明白的话先把上面的几个命令语强行背了。  
多用几次有一天你会明白为什么这么做了...^^

能把merge和rebase玩透了，一般的分支管理就难不到你了。  
如果加上cherry-pick的熟悉运用的话，那就更不得了了...^^  
(但是你只知道这些，仍然是个菜鸟。包括我，哈哈~)

下一遍文章继续介绍cherry-pick的简单应用。  
[git命令cherry-pick的简单应用](https://limingxie.github.io/git/git_cherry_pick/)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`