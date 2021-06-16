---
author: "li_mingxie"
title: "URI,URL,URN的区别"
date:  2021-06-16T08:28:49+08:00
description: "URI,URL,URN的区别"
tags: [
    "URI",
    "URL",
    "URN",
]
categories: [
    "basic"
]
---

我们经常接触到 URI，URL，URN 这些名词。  
本以为都是同一个概念，但我发现它们还是有些区别的。  
先看看各自的单词意思。

```
URI（Uniform Resource Identifier ）：统一资源标识符。
URL（Uniform Resource Locator）：统一资源定位符。
URN（Uniform Resource Name）：统一资源名称。
```

**URI 可以进一步划分为定位符（URL），名称（URN）或两者兼备。  URL 和 URN 都是 URI 子集。**

下面是网上比较流行的结构图...^^

[图片备用地址](https://limingxie.github.io/images/basic/uri_1.png)  
![打开网页的一个过程](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/basic/uri_1.png?x-oss-process=image/resize,w_500,m_lfit)

再看看这个实例区分这三者的关系吧~

#### URL:
* http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/basic/uri_1.png  
* https://www.jianshu.com/u/93eee2dde88b
* tel:+133-3333-3333
* telnet://192.168.0.1:80/

#### URN:
* urn:uuid:9e5bc123-7a1a-3333-9669-0847563c9a88 （一个全局唯一标识符）
* urn:isbn:1234567890（其ISBN编号，以确定一本书）

#### URI
URN + URL ^^;;

PS: 通用 URI 的格式如下  
[协议]://[用户名]:[密码]@[主机名]:[端口]/[路径]?[查询参数]#[片段ID]

```
不知道这么比较合不合适？
URI是身份证，URL是身份证的家庭地址，URN是身份证的姓名
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`