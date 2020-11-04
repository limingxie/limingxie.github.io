---
author: "li_mingxie"
title: "go websocket的简单应用"
date: 2018-12-06T07:28:49+08:00
tags: [
    "go",
    "socket",
    "golang",
]
categories: [
    "Go",
    "golang",
]
---

因为我们是做企业ERP系统，没什么机会接触socket。  
这次准备做消息推送才了解到还有socket这种技术...ㅠㅠ<!--more-->  
(做了这么多年的程序员，连这个也不知道...ㅠㅠ)  
具体的概念就不在这里多做解释了，可以百度或谷歌搜一下。  

我做了一个简单的demo，以便于了解使用方式。  
demo大致构建了如下的场景。  

[图片备用地址](https://limingxie.github.io/images/go_socket/go_socket1.png)  
![1](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go_socket/go_socket1.png)
<!-- ![1](http://localhost:1313/images/go_socket/go_socket1.png)   -->

#### **Dial(用户)**
```go
package main

import (
	"log"

	"golang.org/x/net/websocket"
)

func main() {
    origin := "http://localhost/"
    url := "ws://localhost:5001/test"
    //创建websocket链接
    ws, err := websocket.Dial(url, "", origin)
    if err != nil {
        log.Fatal(err)
    }

    var s string
    for {
        //等待websocket的消息(这个过程是bolck)
        err := websocket.Message.Receive(ws, &s)
        if err != nil {
            log.Println(err)
            ws.Close()
            break
        }
        log.Println(s)
    }
}
```
<br />
#### **Handle(SocketServer)**

```go
package main

import (
	"log"
	"net/http"
	"sync"

	"github.com/labstack/echo"
	"github.com/labstack/echo/middleware"
	"golang.org/x/net/websocket"
)

var socketServer *SocketServer

type SocketServer struct {
	pathPattern       string                    //socket server path
	ClientConnections map[int]*ClientConnection //connect socket server client
	SendmsgCh         chan string               //push message
	mutex             *sync.Mutex
}

type ClientConnection struct {
	id     int
	Ws     *websocket.Conn
	Server *SocketServer
}

func main() {
	socketServer = NewSocketServer("/msg")
	go socketServer.Listen()

	// Echo instance
	e := echo.New()

	// Middleware
	e.Use(middleware.Logger())
	e.Use(middleware.Recover())

	// Routes
	e.POST("/send-message", sendMessage)

	// Start socket server
	go func() {
		log.Fatal(http.ListenAndServe(":5001", nil))
	}()

	// Start api server
	e.Logger.Fatal(e.Start(":5000"))

}

func NewSocketServer(pathPattern string) *SocketServer {
	clients := make(map[int]*ClientConnection)
	sendmsgCh := make(chan string)

	return &SocketServer{
		pathPattern:       pathPattern,
		ClientConnections: clients,
		SendmsgCh:         sendmsgCh,
		mutex:             &sync.Mutex{},
	}
}

// Handler
func sendMessage(c echo.Context) error {
	var v struct {
		Message string `json:"message"`
	}
	if err := c.Bind(&v); err != nil {
		log.Println(err.Error())
	}

	/*
		可以把socketServer放在Middleware。如下获取
		socketServer := c.Value("SocketServer")
		例子我是为了方便直接设成全局变量了
	*/

	for _, clientConnection := range socketServer.ClientConnections {
		//send message to all client
		clientConnection.Server.SendmsgCh <- v.Message
	}

	return c.String(http.StatusOK, "Send OK!")
}

//golang websocket package
func (s *SocketServer) Listen() {
	log.Println("Listening my_server...")

	// websocket handler
	onConnected := func(ws *websocket.Conn) {
		clientConnection := NewClientConnection(ws, s)
		s.mutex.Lock()
		s.ClientConnections[clientConnection.id] = clientConnection
		s.mutex.Unlock()

		for {
			select {
			case msg := <-s.SendmsgCh:
				log.Println(msg)
				websocket.Message.Send(clientConnection.Ws, msg)
			}
		}
	}
	http.Handle(s.pathPattern, websocket.Handler(onConnected))
	log.Println("SocketServer Handler Created")

}

var maxClientConnectionId int = 0

func NewClientConnection(ws *websocket.Conn, server *SocketServer) *ClientConnection {
	if ws == nil {
		panic("ws cannot be nil")
	}

	if server == nil {
		panic("server cannot be nil")
	}

	maxClientConnectionId++

	return &ClientConnection{
		maxClientConnectionId, ws, server}
}
```

**我们可以用postman调用发送信息的api**  
[图片备用地址](https://limingxie.github.io/images/go_socket/go_socket2.png)  
![2](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go_socket/go_socket2.png)
<!-- ![2](http://localhost:1313/images/go_socket/go_socket2.png)   -->

**socket + api server**  
[图片备用地址](https://limingxie.github.io/images/go_socket/go_socket3.png)  
![3](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go_socket/go_socket3.png)
<!-- ![3](http://localhost:1313/images/go_socket/go_socket3.png)   -->

**模拟用户的两个客户端同时接受信息**  
[图片备用地址](https://limingxie.github.io/images/go_socket/go_socket4.png)  
![4](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go_socket/go_socket4.png)
<!-- ![4](http://localhost:1313/images/go_socket/go_socket4.png)   -->

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`