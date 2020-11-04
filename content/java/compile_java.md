---
author: "li_mingxie"
title: "java的编译流程？"
date: 2920-09-28T08:28:49+08:00
tags: [
    "java",
    "class",
    "interface",
    "abstract",
]
categories: [
    "Java",
]
---

如图所示, 先是由 javac 命令把 .java 文件编译成 .class文件。  
.class是可以加载到JVM中运行。当类文件在JVM加载过程中通常会被处理和修改。
转换后的 .class 文件通过解释器解析成可执行代码。
最后由JIT编译器编译成机器码。

[图片备用地址](https://limingxie.github.io/images/java/compile/compile.png)  
![stack](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/compile/compile.png)

#### **先大致的了解一下，等有时间在详细整理。**

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`