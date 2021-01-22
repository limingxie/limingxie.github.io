---
author: "li_mingxie"
title: "【JAVA】打包jar"
date: 2921-01-04T08:28:49+08:00
description: "类加载器的具体结构。"
tags: [
    "java",
    "javac",
    "compile",
    "jar",
]
categories: [
    "java",
]
---

Java 编译是先把xx.java文件编译成xx.class文件。xx.class是个中间状态的字节码(Byte Code)。  
类加载器(Class Loader)读取到JVM里后, 再一次解析成机器码(Binary Code)执行。  
也就是说Java是需要做两次编译，其中的第一次编译(.java => .class)是如何执行的呢？

之前介绍过C语言编译执行是需要4个步骤(预处理，编译，汇编，链接)。(详细的可以参考之前写的[【C语言的编译和执行过程】](https://limingxie.github.io/basic/compile_c/))  

javac的编译(.java => .class)过程一般需要3个步骤。
#### 1. 解析填充符号表Parse and Enter
* 词法分析
* 语法分析
* 填充符号表
#### 2. 插入式注解处理器的注解处理过程Annotation Processing
#### 3. 分析与字节码生产过程Analyse and Generate

这三个步骤之间的关系和交互顺序如下图所示，可以看到如果注解处理器在处理注解期间对语法树进行了修改，编译器将回到解析和填充符号表的过程进行重新处理，直到注解处理器没有再对语法树进行修改为止。

具体流程:

词法分析器：将源码转换为Token流 将源代码划分成一个个Token(找出java语言中的if，else，for等关键字)

语法分析器：将Token流转化为语法树 将上述的一个个Token组成一句句话（或者说成一句句代码块），检查这一句句话是不是符合Java语言规范(如if后面跟的是不是布尔判断表达式)

语义分析器：将语法树转化为注解语法树 将复杂的语法转化成简单的语法（eg.注解、foreach转化为for循环、去掉永不会用到的代码块）并做一些检查，添加一些代码(默认构造器)

代码生成器：将注解语法树转化为字节码(即将一个数据结构转化成另一个数据结构)

这里对于javac编译器做一个简单叙述，感兴趣可以通过OpenJDK来下载源码，然后自己编译javac的源码， 也可以通过调用jdk的com.sun.tools.javac.main.Main类来手动编译指定的类

Sun Javac编译器

 Sun javac编译器编译过程分为三个步骤，解析填充符号表，插入式注解处理器的注解处理过程，分析与字节码生产过程

   **Javac编译器的入口是com.sun.tools.javac.main.JavaCompiler类，主要逻辑集中在compile和compile2方法中**

     
解析填充符号表
词法分析：将源代码的字符流转变为标记（Token）集合   com.sun.tools.javac.parser.Scanner类进行词法分析
语法分析：用标记序列构造抽象语法树（AST，Abstract Syntax Tree）。Eclipse  AST View插件可以用来查看抽象语法树

注解处理器
Java1.5之后提供了对注解（Annotations）的支持，注解处理器可以理解为抽象语法树的一组插件，这些插件可以对抽象语法树直接进行读取，修改，添加操作。
如果在解析注解期间，对语法树进行了修改，那么编译器回到解析及填充符号表的过程重新处理，直到所有的插入式注解处理器没有对语法树进行修改为止。
JavacCompiler 类中initProcessAnnotations()方法对注解处理器进行初始化，JavacProcessingEnvironment类的doProcessing()方法生成新的JavacCompiler对编译的后续过程进行处理。

语义分析与字节码生成
语法分析之后编译器得到程序的抽象语法树表示，语法树表示一个结构正确的源程序抽象，但无法保证源程序是符合逻辑的，语义分析就是对结构正确的源程序上下文进行审查。
语法分析与字节码生成总共分为，标注检查，数据及控制流分析，解语法糖，字节码生成四个过程。
标注检查，和数据及控制流分析主要是对程序上下文环境进行分析检查。
解语法糖：在Java中常用到的解语法糖就是泛型，在Java虚拟机是不支持泛型的。只是通过语法糖进行语法扩展。

#### class文件的结构

#### javac的几个常用关键字
----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`