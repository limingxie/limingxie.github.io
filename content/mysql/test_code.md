---
author: "li_mingxie"
title: "【mysql】常见的4中存储引擎"
date:  2022-01-27T08:28:49+08:00
description: ""
tags: [
    "mysql",
    "InnoDB",
    "MYISAM",
    "Memory",
    "Archive",
]
categories: [
    "mysql#",
    "database",
    "engine",
]
---

我们一般用的mysql创建表的时候默认是使用了InnoBD存储引擎。  
我本人不是计算机专业，所以刚接触mysql时没注意到它居然有好几种存储引擎。  
在不同的场景下，mysql可以选择不同的存储引擎。  <!--more-->  

## 简介
先执行下面的语句，可以看看存储引擎的种类。(基于mysql5.7版本)  

```sql
SHOW ENGINES
```

```
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
```

简单的整理了具有代表意义的4种存储引擎。  
分别是InnoDB， MEMORY，MyISAM，ARCHIVE。  

先做一个试验，看看下面的语句。  

```sql
CREATE DATABASE `test_database`;

USE test_database;

CREATE TABLE `aa_innodb` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(100) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

CREATE TABLE `bb_myisam` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(100) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=latin1;

CREATE TABLE `cc_memory` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(100) DEFAULT NULL
) ENGINE=MEMORY DEFAULT CHARSET=latin1;

CREATE TABLE `dd_archive` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(100) DEFAULT NULL
) ENGINE=ARCHIVE DEFAULT CHARSET=latin1;

```

我们可以看出来，同一个数据库中不同的表可以适用不同的存储引擎。

```sql
SHOW TABLE STATUS FROM test_database
```

```
+------------+---------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-------------------+----------+----------------+---------+
| Name       | Engine  | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         | Update_time         | Check_time | Collation         | Checksum | Create_options | Comment |
+------------+---------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-------------------+----------+----------------+---------+
| aa_innodb  | InnoDB  |      10 | Dynamic    |    0 |              0 |       16384 |               0 |            0 |         0 |           NULL | 2022-01-27 07:25:22 | NULL                | NULL       | latin1_swedish_ci |     NULL |                |         |
| bb_myisam  | MyISAM  |      10 | Dynamic    |    0 |              0 |           0 | 281474976710655 |         1024 |         0 |           NULL | 2022-01-27 07:25:22 | 2022-01-27 07:25:22 | NULL       | latin1_swedish_ci |     NULL |                |         |
| cc_memory  | MEMORY  |      10 | Fixed      |    0 |            106 |           0 |        15878376 |            0 |         0 |           NULL | 2022-01-27 07:25:22 | NULL                | NULL       | latin1_swedish_ci |     NULL |                |         |
| dd_archive | ARCHIVE |      10 | Compressed |    0 |            106 |        8674 |               0 |            0 |         0 |           NULL | NULL                | 2022-01-27 07:25:22 | NULL       | latin1_swedish_ci |     NULL |                |         |
+------------+---------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-------------------+----------+----------------+---------+
```

看看每个存储引擎都有什么特点？

## InnoDB
我们最常用的mysql存储引擎。  
我们说的mysql是关系型数据库，其实就是说mysql的InnoDB引擎。  
支持外间关联，支持事务。  
适合提供提交、回滚、崩溃恢复能力的事物安全（ACID兼容）能力，并要求实现并发控制的业务。  

## MyISAM
MyISAM引擎是一种非事务性的引擎。提供高速存储和检索，特点是支持全文搜索。  
如果数据表主要用来插入和查询记录，则MyISAM引擎能提供较高的处理效率。  
适合数据仓库等查询频繁的应用。   
(我们已经习惯用其他搜索引擎了，比如Elasticsearch...)  

## MEMORY
存在内存里的临时表格。因为是使用内存所以比InnoDB和MYISAM都快，  
但是它的数据是不稳定的，如果在关机之前没有进行保存，所有的数据都会丢失。  
适合数据量不大，并且不需要较高的数据安全性的，查询频繁而且需要较高性能的业务中。  
(我们已经习惯用了Redis了...)  

## ARCHIVE
Archive支持高并发的插入操作，但是本身不是事务安全的。  
只有INSERT和SELECT操作，可以选择Archive，Archive非常适合存储归档数据，如记录日志信息。  
(市面上有很多好用的存放日志的开源系统，还需要用Archive存放日志吗？^^;;)


下图是简单的整理了每个引擎之间的区别，可以参考一下。  

[图片备用地址](https://limingxie.github.io/images/mysql/mysql_engine.png)  
![mysql_engine](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/mysql/mysql_engine.png?x-oss-process=image/resize,w_600,m_lfit)

## 总结
其实我们现在除了InnoDB引擎以外，其他引擎是有更好的替代方案的。  
但是在初期或是发展期，需要过度的时候可以考虑使用这些mysql的引擎。  
毕竟我们想在项目中引入Elasticsearch或redis是会增多很多额外的开发量和费用的。  
前期资源和时间缺乏的时候可以考虑一下这些引擎也是不错的选择。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`