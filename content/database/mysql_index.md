---
author: "li_mingxie"
title: "【mysql】索引简介"
date:  2922-01-27T08:28:49+08:00
description: ""
tags: [
    "mysql",
    "index",
]
categories: [
    "mysql",
    "database",
    "index",
]
---

我们一般用的mysql创建表的时候默认是使用了InnoBD存储引擎。  
我本人不是计算机专业，所以刚接触mysql时没注意到它居然有好几种存储引擎。  
在不同的场景下，mysql可以选择不同的存储引擎。  <!--more-->  

## 简介
先执行下面的语句，可以看看存储引擎的种类。(基于mysql5.7版本)  

按照存储的角度区分

1、聚集索引（clustered index）
聚集索引是按照所以把数据排好序了，所以一个表只能存在一个聚集索引，其它的都是非聚集索引。  
因这个特性，聚集索引是查询数据范围的时候有很大的性能优势。  
但是也需要注意的是如果频繁更新的列不适合设置为聚集索引，  
原因很简单，每次更新都需要从新排序，频繁的更新给的压力也大。  
如果不指定的话，默认主键为聚集索引。  

2、非聚集索引（non-clustered index）
一个表里除了一个聚集索引外其他的都是非聚集索引，虽然不能把数据按照索引排序，但是索引数据是可以排序的。
所以非聚集索引查询范围的时候是先找索引列的范围，再通过这个索引查询行的值。


Primary Key（聚集索引）：InnoDB存储引擎的表会存在主键（唯一非null），如果建表的时候没有指定主键，则会使用第一非空的唯一索引作为聚集索引，否则InnoDB会自动帮你创建一个不可见的、长度为6字节的row_id用来作为聚集索引。
单列索引：单列索引即一个索引只包含单个列
组合索引：组合索引指在表的多个字段组合上创建的索引，只有在查询条件中使用了这些字段的左边字段时，索引才会被使用。使用组合索引时遵循最左前缀集合

Unique（唯一索引）：索引列的值必须唯一，但允许有空值。若是组合索引，则列值的组合必须唯一。主键索引是一种特殊的唯一索引，不允许有空值

Key（普通索引）：是MySQL中的基本索引类型，允许在定义索引的列中插入重复值和空值

FULLTEXT（全文索引）：全文索引类型为FULLTEXT，在定义索引的列上支持值的全文查找，允许在这些索引列中插入重复值和空值。全文索引可以在CHAR、VARCHAR或者TEXT类型的列上创建

SPATIAL（空间索引）：空间索引是对空间数据类型的字段建立的索引，MySQL中的空间数据类型有4种，分别是GEOMETRY、POINT、LINESTRING和POLYGON。MySQL使用

SPATIAL关键字进行扩展，使得能够用于创建正规索引类似的语法创建空间索引。创建空间索引的列必须声明为NOT NULL


[图片备用地址](https://limingxie.github.io/images/database/mysql/mysql_index_1.png)  
![mysql_engine](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/mysql_index_1.png)


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
