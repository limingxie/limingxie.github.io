---
author: "li_mingxie"
title: "Go项目:傻瓜式联合测试"
date: 2019-02-12T08:38:02+08:00
tags: [
    "go",
    "test",
    "join test",
    "golang",
]
categories: [
    "Go",
    "golang",
]
---

现在微服务很流行，加上我的项目从10个表增加到30+表的业务量。  
感觉有些内容不应该在同一个服务里。   
于是我做了一个决定: **`拆分服务`**。<!--more-->  

```
分了4个服务后发现每个服务都不到8个表。每个服务的业务复杂度都不高。  
心理暗自高兴，都说微服务，微服务，原来这么好？

这种好心情维持不到半天，后面出的问题让我晕头转向。
踩了N多个坑...ㅠㅠ
```

今天我想说说其中的测试代码。  
当我分了服务后写单元测试，其实没遇到过什么大问题。  
可以使用上一篇文章介绍的测试替身。   
[Go项目的测试代码3（测试替身Test Double）](https://limingxie.github.io/go/my_go_test3/)

```
问题是每个服务的功能是需要调用其他的服务。   
`单元测试没问题，不能确保联合调用没问题`。  
(初期把单元测试过的代码直接发布运营，把我害惨了...)
```

我做了所有人都能想到的傻瓜式的联合测试方法。  
简单，傻瓜式，但是很实用。
<Br />
<Br />

#### **联合测试思路**
就是把数据库(我是用mysql)和几个服务用docker容器启动，执行联合测试代码。  
这跟本地运行每个服务后，运行测试代码有什么区别吗？(嘿嘿~ 自己想想吧。我也不知道~)
<Br />
<Br />

#### **实际运用例子**
有点linux和docker基础的人，看下面的脚本应该能想到怎么做的吧？

```
#deleted container and image, network
docker rm -f a-service-api-container b-service-api-container... my-mysql 2> /dev/null
docker rmi -f a-service-api-image  b-service-api-image ... 2> /dev/null
docker network rm my-network 2> /dev/null
echo ''
echo '<============== start =============>'

#create docker network
docker network create my-network

echo 'mysql starting ...'
docker run --name my-mysql --network my-network -p 40000:3306 -e MYSQL_ROOT_PASSWORD=1234 -d mysql:latest

export DBSCRIPT=create_db.sql
export MYSQL_PWD=1234

#等待mysql容器正常启动
while true; do
    sleep 1
    date #打印时间，解解闷
    mysqladmin -h 127.0.0.1 -P 40000 -uroot ping 2> /dev/null && break
done

echo "CREATE DATABASE a_database_test CHARACTER SET utf8 COLLATE utf8_general_ci;"  > ${DBSCRIPT}
echo "CREATE DATABASE b_database_test CHARACTER SET utf8 COLLATE utf8_general_ci;"  >> ${DBSCRIPT}
... ...
( mysql -h 127.0.0.1 -P 40000 -uroot < ${DBSCRIPT} || exit ) && rm ${DBSCRIPT}

#先创建a-service-api的镜像
docker build -f ./a-service-api/Dockerfile_jointest --force-rm=true -t a-service-api-image:latest .

#启用容器 需要注意的是，你启动容器的时候，需要多一个添加初始数据的操作
docker run -d --name a-service-api-container -i  -p 40001:8081 \
--network my-network \
-e DATABASE_DRIVER="mysql" \ 
-e DATABASE_CONNECTION="root:1234@tcp(my-mysql)/a_database_test" \
-e ... ...
a-service-api-image:latest

#b-service-api 也一样
docker build -f ./b-service-api/Dockerfile_jointest --force-rm=true -t b-service-api-image:latest .

docker run -d --name b-service-api-container -i  -p 40002:8082 \
--network my-network \
-e DATABASE_DRIVER="mysql" \
-e DATABASE_CONNECTION="root:1234@tcp(my-mysql)/b_database_test" \
-e ... ...
b-service-api-image:latest

... ...
... ...

#之后等待容器正常启动
while true; do
    curl http://localhost:40001/ping 2> /dev/null && break
    sleep 1
done

while true; do
    curl http://localhost:40002/ping 2> /dev/null && break
    sleep 1
done

... ...
... ...


echo ''
echo '>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>'
echo ''
echo 'test start ....... '

#运营你的测试代码
go test ./a-service-api/controllers
go test ./a-service-api/models
go test ./b-service-api/controllers
go test ./b-service-api/models
... ...
... ...
go test ./service-test
echo 'test end!'
echo ''
echo '<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<'


#delete invalid container
docker container prune -f 1> /dev/null
#delete invalid image
docker image prune -f 1> /dev/null
#delete container
docker rm -f a-service-api-container b-service-api-container ... ... my-mysql 1> /dev/null
#delete images
docker rmi -f a-service-api-image  b-service-api-image ... ... 1> /dev/null
#delete invalid network
docker network prune -f 1> /dev/null
docker network rm my-network 2> /dev/null

echo ''
echo '<============== end ===============>'
```

------------

上面的脚本里，创建镜像的时候用到了 Dockerfile 是这么写的。  
(老鸟就不用看了，为了不熟悉的人才上传的)

```
FROM golang:builder AS builder
WORKDIR /go/src/a-service-api/
COPY ./a-service-api/ /go/src/xxxx/a-service-api/

# disable cgo 这个需要注意，因为编译的环境和运行环境不一样的话需要设置这个选项
ENV CGO_ENABLED=0
# build steps
WORKDIR /go/src/xxxx/a-service-api/
RUN echo ">>> 1: go version" && go version
RUN echo ">>> 2: go get" && go get -v -d
RUN echo ">>> 3: go install" && go install
 
# make application docker image use alpine
FROM alpine:3.6
# using timezone
COPY --from=builder /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo "Asia/Shanghai" >  /etc/timezone

# copy config file to image 
RUN mkdir config
COPY --from=builder /go/src/xxxx/a-service-api/config/config.yml ./config/

# copy execute file to image
COPY --from=builder /go/bin/a-service-api/ bin/
EXPOSE 8081

# 需要注意的是，你启动api服务的时候需要先添加联合测试的初始数据以后再启动api服务
CMD ["bin/a-service-api", "join-test"]
```

-------------------

看看执行结果？

```
... ...
... ...
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

test start .......
ok  	xxxx/a-service-api-api/controllers	(cached)
ok  	xxxx/a-service-api-api/models	(cached)
ok  	xxxx/b-service-api-api/controllers	(cached)
ok  	xxxx/b-service-api-api/models	(cached)
... ... 
ok  	xxxx/service-test	(cached)
test end!

<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

<============== end ===============>
```

#### **好了~ 可以发布了...^^** 
------------
备注：  
很多人跟我说这种功能使用 Docker Compose 会很简单，刚开始的时候我也想用过，  
但是我是linux的菜鸟，我想通过这次机会了解一下这些最基础的脚本编写方式。  
实现这些功能，我也断断续续的花了4，5天的时间。  
(因为没基础，出了问题自己折腾大半天也找不出原因，还找公司的大牛帮忙看的...^^;;)  
下次有机会再用 Docker Compose 完善一下吧，哈~  


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
