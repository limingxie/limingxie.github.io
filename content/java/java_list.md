---
author: "li_mingxie"
title: "【Java笔记】简单了解List, ArrayList, LinkedList"
date: 2021-07-16T08:28:49+08:00
description: "List, ArrayList, LinkedList"
tags: [
    "java",
    "Array",
    "ArrayList",
    "LinkedList",
]
categories: [
    "java",
    "jvm",
]
---

我们经常会用的几个List结构，那具体在什么样的场景下使用哪个List会更合适呢？<!--more-->  
我简单整理了一下其内容，比较简单。 想了解更深的内容需要各自的在查一下~  

### 1.List

List是接口(不能直接实例化)，是Collection的子接口。拥有Collection所有方法外，还有一些对索引操作的方法。  
常用的几个方法有  

```java
void add(int index, E element);
boolean addAll(int index, Collection<? extends E> c);
E remove(int index);
int indexOf(Object o);
int lastIndexOf(Object o);
E set(int index, E element);
E get(int index);
List<E> subList(int fromIndex, int toIndex);
void sort(Comparator<? super E> c);
void replaceAll(UnaryOperator<E> operator);
```

### 2.ArrayList

ArrayList 继承了AbstractList，实现了List。  
**ArrayList底层是数组**，数组容量是不可变的，因此ArrayList**需要动态扩容**。在增加操作时，动态扩容会消耗时间。  
由于ArrayList底层是数组，**因此每个元素都带有索引**，在查询效率上要比LinkedList高很多。  

简单的说有以下特点:  
１、List的子类，底层的数据结构使用的是数组结构。  
２、其特点是查询很快，增删改很慢。  
３、其默认的长度为10，如果超过这个范围，则新new一个50%增长速度的数组，再将原数据copy进来。  

### 3.LinkedList

LinkedList继承于AbstractSequentialList，实现了List。  
**LinkedList底层是双向链表**，新增元素就是在链表上新增节点，**不存在扩容的问题**，因此在增删改效率上较高。  
但是由于不存在索引，**每次查询都要遍历整个链表**，查询效率较低。  

简单的说有以下特点:  
１、List的子类，底层的数据结构使用的链表结构。  
２、特点是增删改很快，但是查询的速度很慢。  

### 4.Vector和CopyOnWriteArrayList

ArrayList和LinkedList是好用，但是他们不能保证线程安全。  
所以到多线程的开发模式的话，需要用Vector或者CopyOnWriteArrayList。  
可惜我没机会实际业务上使用过多线程的开发方式。  
具体如何运用打击自行搜索吧...^^;;  

### 5.总结

List是接口类所以不能直接实例化。  
ArrayList底层是数组。更适合查询操作。  
LinkedList底层是链表。更适合修改和增删等操作。  
多线程时需要用Vector或者CopyOnWriteArrayList。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`