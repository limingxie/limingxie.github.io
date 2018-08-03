---
title: "使用docker运行mysql"
date: 2018-03-27T07:28:49+08:00
draft: true
---

以前开发的时候都是用本地的sqlite开发，但是极少数情况下，sqlite支持的语法发布到服务器上链接mysql会报错。

为了避免这种现象，还是链接本地mysql开发还是更稳定的，
可是开发的项目多了后，本地的mysql库看起来好乱。
而且有很多项目是实验性的项目，没有及时处理的话会产生很多垃圾数据。

#### **要不使用docker运行mysql，开发结束后把容器删掉 ?**

>##### **前提是需要准备测试数据脚本**，开发或测试的时候随时可以删除，添加数据。
>##### 这样就不会因为删除docker容器，重新添加数据而烦恼。
>##### 做测试数据很麻烦~ 但是写测试代码，这些测试数据脚本也是必要的。

废话有点多了，说说如何去做。来点干货...^^

**1. 安装docker** (建议上官网下载安装包安装。)

```
$ brew install docker
```

**2. 下载mysql镜像**

```
$ docker pull mysql
```

**3. 启动mysql实例**

```
$ docker run --name mingxie-mysql -p 32xxx:3306 -e MYSQL_ROOT_PASSWORD=1234 -d mysql:latest
```

* ---name 后面的是docker容器名
* -p 32xxx:3306 这里需要注意 `32xxx` 是你**链接mysql的时候的`Port`。**
* -e MYSQL_ROOT_PASSWORD 是设置mysql的root账号密码
* -d mysql 是你的镜像标签

**4. 在shell中访问mysql**

```
docker exec -it mingxie-mysql bash
root@7c289aa0ca95:/# 
mysql -uroot -p -h localhost
Enter password:
```
输入密码即可。

**5. 在shell中访问mysql日志**

```
$ docker logs mingxie-mysql
```

**6. 使用常用工具链接mysql**

    Host: 127.0.0.1
    Username: root
    Password: 1234
    Port: 32xxx

![](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/blog/mingxie-mysql.png)


### 开始敲代码吧...^^

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`