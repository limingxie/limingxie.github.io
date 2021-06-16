---
author: "li_mingxie"
title: "TCP/IP协议，HTTP协议，HTTPS协议，SOAP协议简介"
date:  2021-06-16T18:28:49+08:00
description: "TCP/IP协议，HTTP协议，HTTPS协议，SOAP协议简介"
tags: [
    "TCP",
    "IP",
    "HTTP",
    "HTTPS",
    "SOAP",
]
categories: [
    "basic"
]
---

我们会听到很多网络通讯协议，如果了解的不到位的话会让你晕头转向的。  
今天就按笔记的形式整理了一下我们常见的几种协议。  

先看看通讯协议是什么？ 维基百科是这么解释的。  
## 通信协议（Communications Protocol，传输协议） 
在电信领域中指的是，在任何物理介质中允许两个或多个在传输系统中的终端之间传播信息的系统标准，也是指计算机通信或网络设备的共同语言。  
通信协议定义了通信中的语法学、语义学和同步规则以及可能存在的错误检测与纠正。通信协议在硬件、软件或两者之间皆可实现。

很绕口...^^;; 如果不是技术相关人员的话，可以这么去理解。
```
我和邻居需要沟通，我们定了一些规则。  
比如语言是用中文(一人说中文，一人说外星语是无法沟通的)。  
沟通时都开着大门，站在门口说(一人在前门，一人在后门？)。  
沟通结束后说再见(话没说完，人走掉了？)。  
这些规则确保我们的沟通能正常执行。  
计算机通讯也一样，也是需要一些规则(通讯协议)确保之间的沟通不出问题。
```
我们先看看最重要的TCP/IP协议是什么？

### 1.TCP/IP协议
TCP/IP是个协议组，它是诸多协议的总称。  
可以分为四层：网络接口层（又称链路层）、网络层（又称互联层）、传输层和应用层。  
每一层都呼叫它的下一层所提供的网络来完成自己的需求。  

#### 网络接口层协议：
* Ethernet 802.3  Token Ring 802.5、X.25、Frame relay、HDLC、PPP ATM等。
#### 网络层协议：
* IP（Internet Protocol，英特网协议）  
* ICMP（Internet Control Message Protocol，控制报文协议）  
* ARP（Address Resolution Protocol，地址转换协议）  
* RARP（Reverse ARP，反向地址转换协议）  
#### 传输层协议： 
* TCP（Transmission Control Protocol，传输控制协议）  
* UDP（User Datagram protocol，用户数据报协议）
#### 应用层协议：
* FTP（File Transfer Protocol，文件传输协议）  
* TELNET（用户远程登录服务协议）  
* DNS（Domain Name Service，是域名解析服务）  
* SMTP（Simple Mail Transfer Protocol，简单邮件传输协议）  
* NFS（Network File System，网络文件系统）  
* HTTP（Hypertext Transfer Protocol，超文本传输协议）

#### Internet公用化以后, 使该网络协议在全球应用最广。

### 2.TCP（Transmission Control Protocol，传输控制协议）
TCP是因特网中的**传输层协议**，是给互联网络上提供可靠的端到端字节流而专门设计的一个传输协议。  
使用三次握手协议建立连接，其过程如下：
```
1. 客户端发送SYN（SEQ=x）报文给服务器端，进入SYN_SEND状态。
2. 服务器端收到SYN报文，回应一个SYN （SEQ=y）ACK（ACK=x+1）报文，进入SYN_RECV状态。
3. 客户端收到服务器端的SYN报文，回应一个ACK（ACK=y+1）报文，进入Established状态。
三次握手完成，TCP客户端和服务器端成功地建立连接，可以开始传输数据了。
```
[图片备用地址](https://limingxie.github.io/images/basic/tcp_1.png)  
![tcp三次握手建立连接](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/basic/tcp_1.png?x-oss-process=image/resize,w_500,m_lfit)
### 3.IP（Internet Protocol，英特网协议）
**网际层协议**，是网络之间互连的协议，也是构成互联网的基础。  
IP所提供的服务大致可归纳为两类：
#### 3.1 IP信息包的传送。
为了将IP信息包从源设备(例如用户的计算机)传送到目的设备(例如某部门的www服务器)。IP依赖IP地址与IP路由器两种机制来实现。  
**IP地址**  
IP规定网络上所有的设备都必须有一个独一无二的IP地址，每个IP信息包都必须包含有目的设备的IP地址，信息包才可以正确地送到目的地。  
**IP路由**  
互联网是由许多个网络连接所形成的大型网络。网络之间还需要传送的机制，才能将IP信息包通过一个个的网络传送到目的地。此种传送机制称为IP路由。

#### 3.2 IP信息包的分割与重组。
IP协议在发送IP报文时，一般选择一个合适的初始长度。  
把这报文的数据部分***分割***成若干个较小的数据片，组成较小的报文，然后放到物理帧中去发送。  
每个小的报文称为一个分段。分段的动作一般在路由器上进行。  

***重组***是分段的逆过程，把若干个IP分段重新组合后还原为原来的IP报文。  
一个IP数据报的所有分段都正确地到达目的地，则把它重新组织成一个完整的报文后交给上层协议去处理。

### 4.HTTP（Hypertext Transfer Protocol，超文本传输协议）
Http是**应用层协议**，基于请求/响应模式的、无状态的协议。即我们通常所说的请求Request/响应Response。它通常运行在TCP之上。  
是从WEB服务器传输超文本标记语言(HTML)到本地浏览器的传送协议，设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法。  
当你上网浏览网页的时候，浏览器和Web服务器之间就会通过HTTP在Internet上进行数据的发送和接收。  

[图片备用地址](https://limingxie.github.io/images/basic/http_dns_1.png)  
![打开网页的一个过程](http://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/basic/http_dns_1.png?x-oss-process=image/resize,w_600,m_lfit)

一般http中存在如下问题：  
* 请求信息是明文传输，所以很容易被窃听截取。
* 数据的完整性未校验，所以很容易被篡改
* 没有验证对方身份，所以存在冒充身份的危险

### 5.HTTPS（HyperText Transfer Protocol over Secure Socket Layer）
为了解决上述HTTP存在的问题，后续出来了HTTPS协议。  
一般可以理解为HTTP+SSL/TLS，通过 SSL证书来验证服务器的身份，并为浏览器和服务器之间的通信进行加密。  
可以简单的理解成HTTP中添加了加密验证的方式，弥补上述HTTP中的问题的就是HTTPS。  
至于如何加密和验证，感兴趣的人可以自行查一查~~  
还有socket并不是一种协议, 它是一种链接方式。  
### 6.SOAP（Simple Object Access Protocol）
SOAP是一种简单的基于XML的协议，它使应用程序通过HTTP来交换信息。  
SOAP基于XML语言和XSD标准，其定义了一套编码规则，该规则定义如何将数据表示为消息，以及怎样通过HTTP协议来传输SOAP消息。  
它由以下四部分组成：
+ SOAP信封（Envelope）：定义了一个框架，该框架描述了消息中的内容是什么，包括消息的内容、发送者、接收者、处理者以及如何处理这些消息。
+ SOAP编码规则：它定义了一种系列化机制，用于交换应用程序所定义的数据类型的实例。
+ SOAP RPC表示：它定义了用于表示远程过程调用和应答协定。
+ SOAP绑定：它定义了一种使用底层传输协议来完成在节点间交换SOAP信封的约定。比如 HTTP/TCP/UDP 等等...

把SOAP绑定到HTTP提供了同时利用SOAP的样式和分散的灵活性的特点以及HTTP的丰富的特征库的优点。  
在HTTP上传送SOAP并不是说SOAP会覆盖现有的HTTP语义，而是HTTP上的SOAP语义会自然的映射到HTTP语义。  
在使用HTTP作为协议绑定的场合中，RPC 请求映射到HTTP请求上，而RPC应答映射到HTTP应答。  
然而，在RPC上使用SOAP并不仅限于HTTP协议绑定。SOAP也可以绑定到TCP和UDP协议上。

下面是SOAP消息的基本格式：
```xml
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope
    xmlns:soap="http://www.w3.org/2003/05/soap-envelope"
    soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">
    <soap:Header>
        <!-- 消息头，可选 -->
    </soap:Header>
    <soap:Body>
        <!-- 消息内容，必需 -->

        <soap:Fault>
            <!-- 错误信息，可选 -->
        </soap:Fault>
    </soap:Body>
</soap:Envelope>
```

还有好多好多协议...^^;;  
上面只是我经常接触到的或是说我比较熟悉的协议。  
下次还有机会再整理其他协议。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`