---
author: "li_mingxie"
title: "【Java笔记】String，StringBuffer，Stringbuilder简介"
date: 2921-01-04T08:28:49+08:00
description: "类加载器的具体结构。"
tags: [
    "JVM",
    "java",
    "class loader",
]
categories: [
    "java",
    "jvm",
]
---

StringBuffer，Stringbuilder有什么区别？
StringBuffer与StringBuilder都继承了AbstractStringBulder类，而AbtractStringBuilder又实现了CharSequence接口，两个类都是用来进行字符串操作的。

在做字符串拼接修改删除替换时，效率比string更高。

StringBuffer是线程安全的，Stringbuilder是非线程安全的。所以Stringbuilder比stringbuffer效率更高，StringBuffer的方法大多都加了synchronized关键字

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`