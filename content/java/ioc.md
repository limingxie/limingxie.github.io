---
title: "【java笔记】控制反转(Inversion of Control)"
date: 2099-07-01T07:28:49+08:00
draft: true
---
### **1.基本概念**
这些天因为项目性能上的问题，采用了Redis缓存。  
做了一些简单的笔记。

**◆ Redis**  
Redis(REmote DIctionary Server)是一个key-value存储系统。  
它支持存储的value类型很多，包括string、list、set、hash等等...  
我最喜欢用 key-json字符。

**◆ 应用**  
用的也简单，代码逻辑中读取相应的信息的时候先去Redis读取，  
没有数据，再去数据库(mysql)读取。保存到Redis后，返回结果集。  
因为Redis的数据是保存在内存里的(分保存的方式，暂且可以理解成保存在内存)，  
所以查询速度会很快。  

**◆ 缺点**   
也很容易看到他的缺点，  
1. `一致性问题`。 为了高效查询性能，付出的代价是数据的一致性。  
2. 而且内存的资源是有限的，所以要控制数据量的大小。

**◆ 如何去解决的？**   
如果没有太高的一致性需求的话，  
可以直接`设置比较短的缓存时间`来解决`一致性`的问题。  
比方说缓存时间设置为5分钟，5分钟后数据无效，那再次去数据库查询。

要么做定时任务，每隔一段时间做数据同步。

如果数据一致性需求很高，  
建议每次`数据有变动代码里需要添加删除Redis数据的逻辑`。  
(万一这数据的Mapping关系比较复杂的话，光是删掉这些有关系的数据也会让你哭死...)  
只要删掉了，下次查询会从新去数据库查询数据。

<br />
##### 我这只是简单应用，没什么深度，哈~
##### 下面简单的整理了一下语法
-----------------------
  
**1. 安装Redis** (我比较喜欢下docker镜像安装，如果不想这么做可以搜一下...^^)

```
$ docker run --name test-redis -p 33201:6379 -d redis:latest
```
* ---name 后面的是docker容器名
* -p 32xxx:6379 这里需要注意 `32xxx` 是你**链接redis的时候的`Port`。**
* -d redis:latest 是你的镜像标签和版本

**2. 在shell中访问redis**

```
$ redis-cli -h 127.0.0.1 -p 33201           //链接redis
127.0.0.1:33201> set key01 'hello world'    //赋值
OK
127.0.0.1:33201> get key01                  //查询
"hello world"
127.0.0.1:33201> del key01                  //删除
(integer) 1
127.0.0.1:33201>
```

**2. 设置密码**
```
127.0.0.1:33201> config set requirepass test123     //密码设置为 test123
OK
127.0.0.1:33201> get key01                          //因为设置了密码，验证失败
(error) NOAUTH Authentication required.
127.0.0.1:33201> auth test123                       //登录验证
OK
127.0.0.1:33201> get key01                          //正常
"hello world"
```

**3. 几个常用的命令**

```
#查看所有key
keys *  或  keys "*"

#查看匹配前缀的keys
keys "miao*"

#清空redis
flushdb

#随机取出一个key
randomkey

#查看key的类型
type key

#查看数据库中key的数量
dbsize

#查看服务器信息
info

#查看日志
slowlog get
slowlog get 10
```


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`