---
author: "li_mingxie"
title: "【java笔记】HashMap, HashTable, ConcurrentHashMap, TreeMap, LinkedHashMap"
date: 2021-07-14T12:28:49+08:00
tags: [
    "java",
    "Map",
    "HashMap",
    "HashTable",
    "ConcurrentHashMap",
    "TreeMap",
    "LinkedHashMap",
]
categories: [
    "java"
]
---

我们经常会遇到使用Map的情况。而且Map有很多衍生类。  
这些类之间有什么区别呢？在何种情况下用何种类呢？  
下面我大致整理了什么视情况使用哪种map比较合适，没有单独的去解释每个map的结构和原理。  
如想知道具体结构和原理，需每个都需要单独查一下...^^;;

### 1.HashMap

当我们需要用到key-value存储时，第一个想到的结构就是HashMap。  
HashMap是无序存储。所以不需要顺序的场景下，HashMap是一个很好用的结构。  
使用HashMap时，需注意以下两点。  

#### 自动扩容

HashMap有自动扩容机制。  
> 1.数组默认的长度为16。  
> 2.装载因数默认为0.75。  
> 3.每次扩容操作会增大1倍。  

所以如果要存储大量的数据，需要预估一下数据数量，选择一个合适的初始容量值。  
存储大量数据的过程中会触发多次自动扩容。这些扩容过程需要消耗时间。  
而如果初始设置的容量过大，则可能会浪费内存空间。  

#### 不能保障线程安全

还有HashMap是不能保障线程安全。  
在扩容过程中，如果一个线程进行put或者get操作，此时计算得到的table的下标值可能就不对。  
更糟糕的情况是，若2个线程同时执行put方法，而此时HashMap判断需要进行自动扩容，  
map内部的LinkedList可能会产生环。那么调用get方法时，有可能会掉进死循环中。

所以多线程时需要用ConcurrentHashMap。

### 2.HashTable

HashTable已经被淘汰了，不要在新的代码中再使用它。  
HashTable类的源码注释里有这么一句话。

> If athread-safe implementation is not needed, it is recommended to use
HashMap in place of Hashtable.  If a thread-safe
highly-concurrent implementation is desired, then it is recommended
to use java.util.concurrent.ConcurrentHashMap in place of Hashtable.

大致是这么个意思:  
如果你不需要线程安全，请使用HashMap，如果需要线程安全，请使用ConcurrentHashMap。  

### 3.TreeMap

TreeMap有以下特性。

1. TreeMap内部使用的数据结构为红黑树(Red-Black tree)。
2. TreeMap是有序的key-value集合。  
3. TreeMap不允许key-value为空。  
4. 若Key重复，则后面插入的直接覆盖原来的Value
5. 不能保障线程安全
6. TreeMap不适合用在频繁修改的场景，如果不需要实现有序性，则建议使用HashMap。

看着好复杂...ㅠㅠ  
就记住需要用Key排序的情形，请使用TreeMap。  

### 4.LinkedHashMap

LinkedHashMap直接继承自HashMap，所以LinkedHashMap拥有HashMap的大部分特性。  
一些主要的方法和属性也直接继承自HashMap，并对其中某些方法进行了重写。  
LinkedHashMap与HashMap最大的不同在于LinkedHashMap保持了元素的有序性。  
即遍历LinkedHashMap的时候，得到的元素的顺序与添加元素的顺序是相同的历。  

### 5.ConcurrentHashMap

使用HashMap不能保障线程安全，使用HashTable性能又不行。  
所以从Java5开始，Java提供了适用于多线程环境的。  
因本人没有用java写过高并发多线程的代码...ㅠㅠ  
这里就不装懂了，我只能说需要多线程的场景请使用ConcurrentHashMap。  


### 总结

单线程时，不需要顺序的场景下使用HashMap。  
需要用Key排序，使用TreeMap。  
需要用添加数序，使用LinkedHashMap。

多线程时，使用ConcurrentHashMap。

HashTable已被淘汰，尽量别使用。


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`