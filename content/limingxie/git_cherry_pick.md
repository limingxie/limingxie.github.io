---
title: "git命令 cherry-pick 的使用简介"
date: 2118-07-30T07:28:49+08:00
draft: true
---
上一篇文章介绍了merge和rebase的用法。   
[git命令merge和rebase的简单应用](https://limingxie.github.io/limingxie/git_merge_rebase/)

代码分几个大分支，各自有不同的功能在开发，这时候我们就有这样的需求。  
希望`只合并某个分支上的某次修改commit到指定的分支上`。这时候可以使用`cherry-pick命令`来操作。

如何起实现呢？
我做了一些简单的试验，分享一下。

场景是这样的。  
创建一个11.txt文件。  
[图片备用地址](https://limingxie.github.io/images/git_cherry_pick/cherry_pick1.png)  
![0](http://localhost:1313/images/git_cherry_pick/cherry_pick1.png)


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`