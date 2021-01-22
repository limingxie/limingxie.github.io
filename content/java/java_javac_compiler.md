---
author: "li_mingxie"
title: "【JVM】javac的编译过程"
date: 2021-01-04T08:28:49+08:00
description: "类加载器的具体结构。"
tags: [
    "java",
    "javac",
    "compile",
]
categories: [
    "java",
]
---

Java 编译是先把xx.java文件编译成xx.class文件。xx.class是个中间状态的字节码(Byte Code)。  
类加载器(Class Loader)读取到JVM里后, 再一次解析成机器码(Binary Code)执行。  
也就是说Java是需要做两次编译，其中的第一次编译(.java => .class)是如何执行的呢？

之前介绍过C语言编译执行是需要4个步骤(预处理，编译，汇编，链接)。  
(详细的可以参考之前写的[【C语言的编译和执行过程】](https://limingxie.github.io/basic/compile_c/))  
那javac的编译是如何进行的呢？  

>javac的编译(.java => .class)过程大体上可以分3个步骤。
>1. 解析填充符号表Parse and Enter
>2. 插入式注解处理器的注解处理过程Annotation Processing
>3. 分析与字节码生产过程Analyse and Generate

这三个步骤之间的关系和交互顺序如下图所示:  
可以看到如果注解处理器在处理注解期间对语法树进行了修改，编译器将回到解析和填充符号表的过程进行重新处理。  
直到注解处理器没有再对语法树进行修改为止。(不太明白也没关系，下面有详细的解释。)

[图片备用地址](https://limingxie.github.io/images/java/jvm/javac_compile.png)  
![javac_compile](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/jvm/javac_compile.png?x-oss-process=image/resize,h_210,m_lfit)

下面详细的介绍一下每个过程是如何进行的。

### 1. 解析填充符号表Parse and Enter
首先会做词法分析，语法分析以及填充符号表。  

#### 1.1 词法分析

将源代码的字符流转变为最小单元的标记（Token）集合。  
比如如“int a = b + 1”这句代码包含了 int、a、=、b、+、1 => 6个标记。  

#### 1.2 语法分析
这里就对token流做语法分析了。比如if后面接的是不是返回值为bool的表达式之类的。  
然后把符合规范的语法构造抽象语法树（AST，Abstract Syntax Tree）。  

#### 1.3 填充符号表
符号表是有一组符号地址和符号信息构成的表格。  
填充符号表的过程的出口是一个待处理列表，包含了每一个抽象语法树（和package-info.java）的顶级节点。  

### 2. 插入式注解处理器的注解处理过程Annotation Processing
注解处理器可以理解为抽象语法树的一组插件，这些插件可以对抽象语法树直接进行读取，修改，添加操作。  
如果在解析注解期间，对语法树进行了修改，那么编译器回到解析及填充符号表的过程重新处理，  
直到所有的插入式注解处理器没有对语法树进行修改为止。  

有了编译器注解处理的标准API后，我们的代码才有可能干涉编译器的行为，  
由于语法树中的任意元素，甚至包括代码注释都可以在插件之中访问到，  
所以通过插入式注解处理器实现的插件在功能上有很大的发挥空间。  
只要有足够的创意，程序员可以使用插入式注解处理器来实现许多原本只能在编码中完成的事情。

### 3. 分析与字节码生产过程Analyse and Generate
这部分看源代码的话(可以参考下面我截图的源代码compile2函数)可以分:标注检查，数据和控制流分析，解析语法糖以及生成字节码。  
很多时候把标注检查，数据和控制流分析，解析语法糖合起来说语义分析。  

#### 3.1 语义分析
语法分析后可以保证形成语法树以后不存在语法错误，但无法保证源程序是符合逻辑，所以需要对源程序上下文进行审查。  
还有Java会有一些相对复杂的语法，语义分析的作用就是将这些复杂的语法翻译成更简单的语法。  

**3.1.1 标注检查：**  
包括变量使用前是否已声明, 变量与赋值之间的数据类型是否匹配, 常量折叠：int a = 1  + 2 ==> int a = 3

**3.1.2 数据流分析和控制流分析：**  
数据流及控制流的分析入口是flow()方法，具体操作由com.sun.tools.javac.comp.Flow类来完成。  

_数据流分析_  
局部变量是否赋值，final修饰的变量不会被重复赋值，方法路径返回值验证，受检异常的正确处理，所有的语句是否都要被执行等等...  

_控制流分析_  
去掉无用的代码，比如永假的if代码块，变量的自动转换，比如自动装箱拆箱等等...  

**3.1.3  解语法糖：**  
泛型，装箱拆箱，for循环，条件编译等等等...  
解语法糖的过程由desugar()方法触发，在com.sun.tools.javac.comp.TransTypes和com.sun.tools.javac.comp.Lower类中完成。

#### 3.2 字节码生成：
字节码生成是 javac 编译过程的最后一个阶段。  
这一阶段还进行少量的代码添加和转换工作。  
比如实例构造器方法和类构造器方法就是在这个阶段添加到语法树之中。  
这里的实例构造器并不是指默认的构造函数，而是指我们自己重载的构造函数。   
完成了对语法树的遍历和调整之后，就会把填充了所有所需信息的符号表交给com.sun.tools.javac.jvm.ClassWriter类，  
由这个类的writeClass()方法输出字节码，生成最终的class文件。

### 源代码
百闻不如一见，上面啰里啰嗦的说了一大堆不如看javac的源代码理解的会更快，  
Javac编译动作的入口是com.sun.tools.javac.main.JavaCompiler类，  
上述3个过程的代码逻辑集中在这个类的compile()和compile2()方法中，  

[图片备用地址](https://limingxie.github.io/images/java/jvm/javac_compile_code.png)  
![javac_compile](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/jvm/javac_compile_code.png?x-oss-process=image/resize,w_880,m_lfit)


## 总结
按照顺序我们可以这么简单的理解javac的编译过程。
1. 词法分析 => 把源代码转变为token
2. 语法分析 => 使用token生成抽象语法树
3. 语义分析 => 做一些逻辑上的验证，优化语法树
4. 生成字节码 => 调整完语法树后生成最终class文件。

----------------------------------------------

### 题外话: class文件
从.java源文件辛辛苦苦编译成.class文件。那.class文件的结构又是如何呢？  
先写一个经典HelloWorld程序:
```java
public class ClassTest {
    public static void main(String[] args) {
        System.out.println("hello world!");
    }
}
```

使用javac命令编译以后，用 vi 命令查看一下里面的内容  

[图片备用地址](https://limingxie.github.io/images/java/jvm/javac_class1.png)  
![javac_class](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/jvm/java_class1.png)

看不明白...^^;;   
好像只能看出来初始化引用包以及我写的类名和大致的内容。  
可以用IDEA直接打开.class文件查看里面的内容，会看的很舒服。  

[图片备用地址](https://limingxie.github.io/images/java/jvm/javac_class1.png)  
![javac_class](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/jvm/java_class2.png)

里面具体的结构就去查其他资料吧...^^;;  
还没研究过这里面的东西 哈~


<!-- 词法分析
词法分析过程是在的JavacParser.parseCompilationUnit()中完成的
com.sun.tools.javac.parser.JavacParser　　规定哪些词符合Java语言规范，具体读取和归类不同词法的操作由scanner完成
com.sun.tools.javac.parser.Scanner　　负责逐个读取源代码的单个字符,然后解析符合Java语言规范的Token序列，调用一次nextToken()都构造一个Token
com.sun.tools.javac.parser.Tokens$TokenKind　　里面包含了所有token的类型，譬如BOOLEAN,BREAK,BYTE,CASE。
com.sun.tools.javac.util.Names　　用来存储和表示解析后的词法，每个字符集合都会是一个Name对象，所有的对象都存储在Name.Table这个内部类中。
com.sun.tools.javac.parser.KeyWords　　负责将字符集合对应到token集合中，如，package zxy.demo.com; Token.PACKAGE = package， Token.IDENTIFIER = zxy.demo.com,(这部分又分为读取第一个token,为zxy，判断下一个token是否为“.”，是的话接着读取下一个Token.IDENTIFIER类型的token，反复直至下一个token不是”.”,也就是说下一个不是Token.IDENIFIER类型的token，Token.SEMI = ；即这个TIDENTIFIER类型的token的Name读完），KeyWords类负责此任务。
语法分析：
com.sun.tools.javac.tree.TreeMaker　　所有语法节点都是由它生成的，根据Name对象构建一个语法节点
com.sun.tools.javac.tree.JCTree$JCIf 　　所有的节点都会继承jctree和实现＊＊tree，譬如 JCIf extends JCTree.JCStatement implements IfTree
com.sun.tools.javac.tree.JCTree的三个属性
语义分析
com.sun.tools.javac.comp.Enter　　将java类中的符号输入到符号表中，主要是两个步骤：
将所有类中出现的符号输入到类自身的符号表中，所有类符号、类的参数类型符号（泛型参数类型）、超类符号和继承的接口类型符号等都存储到一个未处理的列表中。
将这个未处理的列表中所有的类都解析到各自的类符号列表中，这个操作是在MemberEnter.complete()中完成(默认构造器也是在这里完成的)。
com.sun.tools.javac.processing.JavacProcessingEnvironment　　处理注解
com.sun.tools.javac.comp.Attr　　检查语义的合理性并进行逻辑判断，类型是否匹配，是否初始化，泛型是否可推导，字符串常量合并
com.sun.tools.javac.comp.Check　　协助attr，变量类型是否正确
com.sun.tools.javac.comp.Resolve　　协助attr，变量方法类的访问是否合法，是否是静态变量
com.sun.tools.javac.comp.ConstFold　　协助attr，常量折叠
com.sun.tools.javac.comp.Infer　　协助attr，推导泛型
com.sun.tools.javac.comp.Flow　　数据流分析和替换等价源代码的分析（即上面的进一步语义分析）
代码生成
com.sun.tools.javac.jvm.Gen　　遍历语法树生成最终的java字节码
com.sun.tools.javac.jvm.Items　　辅助gen，这个类表示任何可寻址的操作项，这些操作项都可以作为一个单位出现在操作栈上
com.sun.tools.javac.jvm.Code　　辅助gen，存储生成的字节码，并提供一些能够影射操作码的方法 -->

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`