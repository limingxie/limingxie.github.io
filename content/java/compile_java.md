---
author: "li_mingxie"
title: "【JVM】java大致的编译过程"
date: 2020-11-20T08:28:49+08:00
tags: [
    "java",
    "jvm",
    "compile",
]
categories: [
    "Java",
]
---

我们先简单的了解一下java编译到执行的过程。  
后续我会详细的介绍具体详细的方式。<!--more-->

[图片备用地址](https://limingxie.github.io/images/java/compile/java_simple_complie.png)  
![stack](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/compile/java_simple_complie.png)

当我们执行代码的时候是如图所示，执行以下步骤。  

1. Java Source(xxx.java file) 文件 通过 javac命令编译成。  

2. 经过一些验证和编译创建Java Byte Code(xxx.class file)。xxx.class文件不是完全的机器语言，所以电脑不能直接读取文件。  

3. 使用java命令执行 xxx.class文件。  

4. JVM通过类加载器(class loader)读取xxx.class文件。  

5. Byte Code Verfier 先验证xxx.class文件是否合法。  

6. 解释器(Interpreter)把xxx.class文件转换成Binary Code。  

7. Runtime执行。  

#### **先可以这么大致的理解这部分的过程。**
#### **后续我会详细的整理详细的内容。**

## **未完待续**
----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`