---
title: "区块链blockchain基础入门(二)"
date: 2018-06-22T07:28:49+08:00
draft: true
---

上一篇文章简单的介绍了一下区块链blockchain到底是什么。   
[区块链blockchain零基础入门(一)](https://limingxie.github.io/limingxie/blockchain_introduction/)

接下来继续简单的介绍一下区块链blockchain的结构。

### **◆ 区块Block + 链Chain**  
 
区块链blockchain是`由一个个区块block组成`，每次交易的数据是写入到一个区块block中。  
每个区块是由区块头Heather和去块体Body组成。
```
区块头Heather: 包含着生成时间，区块体Body的地址(Hash值)，以及上一个区块block的地址(Hash值)。
区块体Body: 详细的数据。
```

下图是简单的结构

[图片备用地址](https://limingxie.github.io/images/blockchain_2/block_chain.png)
![0](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/block_chain/block_chain.png)

实际的Header的结构是这样的，不是IT行业的话可以略过下图。  
[图片备用地址](https://limingxie.github.io/images/blockchain_2/block_chain1.png)
![1](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/block_chain/block_chain1.png)

如上图所示，每个区块Block都保存前一个区块Block的地址(Hash值)。  
这样我们就可以把每个`区块block`连接到上一个区块Block，组成很长的`链chain`。  
这就是`区块链blockchain`名字的由来。

--------------------------

### **◆ 节点node**  
区块链blockchain中的数据是`分布式存储`。  
这些数据会分布在不同的地方存储，存储数据的每一个点我们叫它为节点node。  
(仅仅是在区块链blockchain中是可以这么理解)。

以比特币为例(比特币是第一个成功的区块链blockchain技术)。  
比特币`网络是由很多个节点node组成`，而且`每个节点node都保存所有的交易数据`。  
###### (这会导致很多的重复数据，我感觉这是一种浪费。哈~ )  

<Br/>
打个比方，每个节点node都拥有同样的账本。  
当某一个`节点node增加新的交易数据(新的区块block)`。  
它会把这份交易数据(新的区块block)分发给各各节点node，  
其他节点node收到账本的新一页(新的区块block)后，  
把`这份交易数据(新的区块block)放在自己账本(整个交易数据)的最后一页`。  
这样确保所有的节点node的账本是一致的。==> (`分布式存储`)

[图片备用地址](https://limingxie.github.io/images/blockchain_2/block_chain2.png)
![2](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/block_chain/block_chain2.png)

-----------------------------

### **◆ 挖矿mining** 

那万一2个以上的节点node同时创建新的区块block岂不是乱了吗？  
这里牵扯到区块认证。比特币是大约10分钟一次创建新的区块block。  
在这10分钟内的交易数据是以`工作量证明(PoW)Proof-Of-Work`方式，获得新建区块block权利。  

什么意思？不太明白？  
简单的说想要在区块链blockchain中添加新的区块block的话，  
你需要要获得创建新区块Block的权利。  
    
    怎么获取添加新建区块block的权利呢？  
    算一道题。 
    什么样的题？ 
    
    可以理解成一个猜数字游戏。1到10000中有一个数据是正确的让你去猜。  
    怎么办？没办法，只能暴力破解。

    是不是5000？ 不是! 是不是5301？ 不是!   
    是不是xxx ？ 不是！ 是不是xxx ？。。。

这题猜中的偶然性很高，也有运气的成分。  
但是如果你的服务器性能好的话，猜的次数越多，猜对的可能性也变得高。  

`猜对了，获得权利。你可以创建新的区块block，分发到网络`。  
大声告诉大家: “这道题我猜对了，我已经新建了一个区块block了，你们不用再算了。”    
然后把自己建好的区块block分发给其他的节点node。

**难度系数difficulty**

    读到这里，你可能会有一个疑问，上面说平均10分钟创建一个区块block。
    算法题万一5分钟就猜到了，或是15分钟后才能猜到怎么办呢？
    难度系数difficulty就这么登场了。它包含在区块头。
    
    当出的题是1到10万当中猜一个看看？ 这时候难度系数是10。
    发现5分钟就猜到了，那怎么办？
    没关系，我调一下难度系数，调到20。
    
    下次出题：1到20万当中猜一个看看？
    这次15分钟后才道了， 再调成15。

    下次出题：1到15万当中猜一个看看？

并非是这么简单，但是流程上是可以这么理解的。

**报酬**  
那为什么这么多节点抢创建区块block的权利呢？  
嘿嘿，谁创建了区块block，系统会给他12.5个比特币作为报酬。  
(1个比特币现在6千多美元，可以算算值多少钱...^^)

    比特币刚出来的时候给的报酬是50个比特币(赚翻了...^^)
    每开采210,000个新区块(大概需要4年) 给的报酬会减半。
    以此类推。大约到2140年的时候，区块链发行完毕，大约2100万个比特币。
    这就是比特币的总量，所以不会无限增加下去。

这就是我们经常听到的`挖矿mining`。  
为了获得创建区块block权利，创建区块block而获得报酬，  
很多很多的矿机为了这个报酬，不分昼夜的算啊算啊~
###### (又是浪费电费，磨损服务器，就为了一个虚拟的币。对社会没有一点贡献...^^|| )  

[图片备用地址](https://limingxie.github.io/images/blockchain_2/block_chain3.jpeg)  
![3](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/block_chain/block_chain3.jpeg)

<Br/>
你投资很多钱买了挖矿机，运气差~ 一次也没抢到。那不亏死了？  
有很多平台，将这些服务器组建成一个集群，一起算。  
算出来的话，以贡献的算率来分获取的比特币。  
这就是有些人说挖矿，挖了0.0xx的比特币的原因。而不是12.5个。


    并非所有的区块链blockchain都会采用挖矿mining的`工作量证明(PoW)Proof-Of-Work`方式。  
    也有`权益证明(PoS)Proof-of-Stake`方式，  
    `股份授权证明(DPoS)Delegate Proof of Stake`方式等等 。。。 

写的有点多了，下次有时间的话再详细的解释其原理和内部结构。

### **继续更新**

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`