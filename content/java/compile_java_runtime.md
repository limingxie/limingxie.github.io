---
author: "li_mingxie"
title: "【JVM】Execution Engine执行引擎"
date: 2120-11-20T08:28:49+08:00
tags: [
    "java",
    "jvm",
    "compile",
    "runtime",
    "Interpreter",
    "JIT",
    "mixed",
    "compile",
]
categories: [
    "Java",
]
---

JVM이 자바 실행파일을 기계어 코드로 변환하는 방식에는
인터프리터
JIT (Just In Time) compiler
이 있습니다.

JVM종류
JVM기능을 구현한 여러 JVM이 있습니다. 대표적인 JVM으로 Hotspot, JRockit이 있는데 합쳐지고 있다고 합니다.
Java HotSpot VM은 두 가지로 나뉩니다.
Java HotSpot Client VM — 응용 프로그램 시작 시간과 메모리 사용 공간을 줄임으로써 클라이언트 환경에서 응용 프로그램을 실행할 때 최상의 성능을 발휘하도록 조정되었습니다.
Java HotSpot Server VM — 서버 환경에서 실행되는 응용 프로그램의 최대 프로그램 실행 속도를 위해 설계되었습니다.


HotSpot JVM에서 JIT컴파일
핫스팟 JVM은 JIT컴파일과 최적화를 수행합니다. 실행시 바로 컴파일하지않고 인터프리터가 먼저 동작하며 자주 사용되는 부분만 컴파일합니다. 컴파일 된 부분은 인터프리터보다 훨씬 빨리 실행됩니다. 자주 실행하는 부분에 적용되면, 프로그램 실행속도를 크게 단축할 수 있습니다.

我们知道Java文件编译后生成.class文件后。  
类加载器class loader 加载这些文件到JVM的内存中。  
通过执行引擎Execution Engine分配到JVM内存的的(Runtime Data Area)区域。<!--more-->

类加载器将字节码载入内存后，执行引擎以java字节码为单元，读取java字节码。
java字节码机器读不懂，必须将字节码转化为平台相关的机器码。这个过程就是由(Execution Engine)执行引擎完成的。

执行引擎(Execution Engine)有三种执行模式。
1.解释执行, 2.JIT编译执行, 3.混合执行。

1.解释执行模式
解释执行是会一行一行的读取代码后执行
2.JIT编译执行模式
为了弥补解释执行的执行速度慢的问题，出来的是编译执行。
先解释执行，当满足一定条件的时候直接把代码编译到指定缓存里，下一次执行的时候不需要再次解析编译了。
所以比起解释执行速度会快很多，但是如果只是偶尔执行一次的代码的话，解释执行比编译执行更合适。
3.混合执行模式


## **未完待续**
----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`