---
title: "Java Platform SE 8 的简单介绍"
date: 2020-10-26T08:28:49+08:00
draft: true
---

刚接触到java后，我经常听到的JDK，JRE，JVM这些缩写词。  
对我这个java菜鸟来说有时候会没来得及反应过来..^^;;    
借助这次机会好好地整理和明确一下这些概念。  

## **Description of Java Conceptual Diagram**  
Oracle提供两个Java平台标准版(Java SE)8的产品:  
Java SE开发工具包(JDK)8 和 Java SE运行时环境(JRE)8。  
他两的区别是什么？  

先看一下官方提供的结构图，看这个图大部分的结构会看的很清楚的。  
(下面的解释是不是多余的了? ^^!!)  

[图片备用地址](https://limingxie.github.io/images/java/jvm/description_of_java_conceptual_diagram.png)  
![description_of_java_conceptual_diagram](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/description_of_java_conceptual_diagram.png)  
<div align='center' >图片来源: [https://docs.oracle.com/javase/8/docs/index.html](https://docs.oracle.com/javase/8/docs/index.html)</div>

#### **一. JDK（Java Development Kit，Java开发工具包）**  
JDK包含了java所有的内容，包含了JRE和JVM。  
比JRE包含了更多的是开发需要的的工具，比如javac，javadoc等等...

#### **二. JRE（Java Runtime Environment， Java运行环境）** 
是Java平台，所有的程序都要在JRE下才能够运行。  
包括JVM和Java核心类库和支持文件。 
因为是运行环境，所以比起JDK少了那些开发工具。  

#### **三. JVM（Java Virtual Machine， Java虚拟机）** 
无论JDK和JRE都包含着JVM。  
因为java编译后的字节码是通过JVM和运行环境的操作系统做互动的。  
Java可以跨平台也是因为Java程序和操作系统中间夹着这个JVM。
不同的操作系统会有不同的JVM映射规则，完成跨平台性。

#### **四. JDK, JRE, JVM的相互关系** 
我们开发系统的时候需要安装JDK, 因为JDK包含着开发需要的开发工具。  
开发完成之后的运行环境(正式服务器)就不需要JDK了，只有安装JRE即可。  
开发完成的代码编译成字节码以后，通过JVM和真实操作系统互动。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`