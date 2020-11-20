---
author: "li_mingxie"
title: "【JVM】JIT编译器"
date: 2920-11-05T08:28:49+08:00
tags: [
    "java",
    "compile",
    "jit",
    "jvm",
]
categories: [
    "Java",
]
---

JIT(Just In Time Compiler)编译器
当JVM发现某个方法或代码块运行特别频繁时，就会把这些代码认定为“热点代码（Hot Spot Code）”，然后JVM会把这些代码编译成与本地平台相关的机器码，并进行各种层次的优化，完成这个任务的编译器称为：即时编译器（Just In Time Compiler，JIT）

2.3 编译器 - Compiler
只说HotSpot JVM

1. C1和C2：
HotSpot虚拟机内置了两个即时编译器，分别称为Client Compiler和Server Compiler，习惯上将前者称为C1，后者称为C2

它们俩也是JVM的分类Client VM和Server VM

HotSpot默认采用解释器和其中一个编译器直接配合的方式工作，使用那个编译器取决于虚拟机运行的模式，HotSpot会根据自身版本和宿主机器硬件性能自动选择模式，用户也可以使用“-client”或”-server”参数去指定

无论VM是Client模式还是Server模式，还有一类模式是根据使用解释器和编译器的方式来划分的：

1. 混合模式（Mixed Mode）
默认的模式，如上面描述的这种方式就是mixed mode

2. 解释模式（Interpreted Mode）
可以使用参数“-Xint”，在此模式下全部代码解释执行

3. 编译模式（Compiled Mode）
参数“-Xcomp”，此模式优先采用编译，但是无法编译时也会解释（在最新的HotSpot中此参数被取消）


HotSpot 内置两种编译器，分别是client启动时的c1编译器和server启动时的c2编译器，c2在将代码编译成机器代码的时候需要搜集大量的统计信息以便在编译的时候进行优化，因此编译出来的代码执行效率比较高，代价是程序启动时间比较长，而且需要执行比较长的时间，才能达到最高性能；与之相反， c1的目标是使程序尽快进入编译执行的阶段，所以在编译前需要搜集的信息比c2要少，编译速度因此提高很多，但是付出的代价是编译之后的代码执行效率比较低，但尽管如此，c1编译出来的代码在性能上比解释执行的性能已经有很大的提升，所以所谓的分层编译，就是一种折中方式，在系统执行初期，执行频率比较高的代码先被c1编译器编译，以便尽快进入编译执行，然后随着时间的推移，执行频率较高的代码再被c2编译器编译，以达到最高的性能。

在 Java8 中，默认开启分层编译，-client 和 -server 的设置已经是无效的了。如果只想开启 C2，可以关闭分层编译（-XX:-TieredCompilation），如果只想用 C1，可以在打开分层编译的同时，使用参数：-XX:TieredStopAtLevel=1。

你可以通过 java -version命令行可以直接查看到当前系统使用的编译模式

mixed mode代表是默认的混合编译模式，除了这种模式外，我们还可以使用-Xint参数强制虚拟机运行于只有解释器的编译模式下，这时 JIT 完全不介入工作；也可以使用参数-Xcomp强制虚拟机运行于只有 JIT 的编译模式下。例如：

C:\Users\Administrator>java -Xint -version
java version "1.8.0_45"
Java(TM) SE Runtime Environment (build 1.8.0_45-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.45-b02, interpreted mode)

C:\Users\Administrator>java -Xcomp -version
java version "1.8.0_45"
Java(TM) SE Runtime Environment (build 1.8.0_45-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.45-b02, compiled mode)

触发标准
在 HotSpot 虚拟机中，热点探测是 JIT 的触发标准。
热点探测是基于计数器的热点探测，采用这种方法的虚拟机会为每个方法建立计数器统计方法的执行次数，如果执行次数超过一定的阈值就认为它是“热点方法” 。
虚拟机为每个方法准备了两类计数器：方法调用计数器（Invocation Counter）和回边计数器（Back Edge Counter）。在确定虚拟机运行参数的前提下，这两个计数器都有一个确定的阈值，当计数器超过阈值溢出了，就会触发 JIT 编译。

方法调用计数器
方法调用计数器用于统计方法被调用的次数，默认阈值在 C1 模式下是 1500 次，在 C2 模式在是 10000 次，可通过-XX: CompileThreshold来设定；而在分层编译的情况下-XX: CompileThreshold指定的阈值将失效，此时将会根据当前待编译的方法数以及编译线程数来动态调整。当方法计数器和回边计数器之和超过方法计数器阈值时，就会触发 JIT 编译器。

回边计数器
回边计数器用于统计一个方法中循环体代码执行的次数，在字节码中遇到控制流向后跳转的指令称为“回边”（Back Edge），该值用于计算是否触发 C1 编译的阈值，在不开启分层编译的情况下，C1 默认为 13995，C2 默认为 10700，可通过 -XX: OnStackReplacePercentage=N来设置；而在分层编译的情况下，-XX: OnStackReplacePercentage指定的阈值同样会失效，此时将根据当前待编译的方法数以及编译线程数来动态调整。
建立回边计数器的主要目的是为了触发 OSR（On StackReplacement）编译，即栈上编译。在一些循环周期比较长的代码段中，当循环达到回边计数器阈值时，JVM 会认为这段是热点代码，JIT 编译器就会将这段代码编译成机器语言并缓存，在该循环时间段内，会直接将执行代码替换，执行缓存的机器语言。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`