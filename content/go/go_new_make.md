---
author: "li_mingxie"
title: "【go笔记】new和make的区别"
date: 2021-07-27T07:28:49+08:00
tags: [
    "go",
    "golang",
    "new",
    "make",
]
categories: [
    "Go",
    "golang",
]
---

new和make都是分配资源的命令，这两个命令很容易混淆。  
我们只要弄清楚其规则，很容易分清和区分。

### 1. new

先看看go语言的官方说明文档是怎么解释的？

```
new是用来分配内存的内建函数，但与其它语言中的同名函数不同，它不会初始化内存，只会将内存置零。  
也就是说，new(T) 会为类型为T的新项分配已置零的内存空间，并返回它的地址，也就是一个类型为 *T的值。  
用Go的术语来说，它返回一个指针，该指针指向新分配的，类型为 T 的零值。  
```

简单的说他会创建一个引用类型的地址(栈内存)，不会分配内存空间(堆内存)。  
看看是不是真的是这样？  

```go
package main

import (
	"fmt"
	"reflect"
)

type User struct {
	Id   int
	Name string
}

func main() {
	user := new(User)
    fmt.Printf("type:   %+v \n", reflect.TypeOf(user))
	fmt.Printf("value:  %+v \n", user)

	var user1 User
    fmt.Printf("type:   %+v \n", reflect.TypeOf(user1))
	fmt.Printf("value:  %+v \n", user1)


	user2 := User{}
    fmt.Printf("type:   %+v \n", reflect.TypeOf(user2))
	fmt.Printf("value:  %+v \n", user2)
}
```
看一下执行结果
```
$ go run main.go
type:   *main.User      //new 方式
value:  &{Id:0 Name:}   //new 方式
type:   main.User       //var 声明
value:  {Id:0 Name:}    //var 声明
type:   main.User       //快速 声明
value:  {Id:0 Name:}    //快速 声明
```

#### new分配的关键点是配置了零内存。  
#### 按照我们的通俗的语言说：new返回了指针。

----------------------------

### 2. make

先看看go语言的官方说明文档是怎么解释的？

```
make用于创建切片、映射和信道，并返回类型为 T（而非 *T）的一个已初始化 （而非置零）的值。  
出现这种用差异的原因在于，这三种类型本质上为引用数据类型，它们在使用前必须初始化。  
例如，切片是一个具有三项内容的描述符，包含一个指向（数组内部）数据的指针、长度以及容量， 在这三项被初始化之前，该切片为 nil。  
对于切片、映射和信道，make 用于初始化其内部的数据结构并准备好将要使用的值。
```

make是只用于切片(slice)、映射(map)和信道(channel)。 而且是已初始化 （而非置零）的值。  
看看是不是真的是这样？

```go
package main

import (
	"fmt"
	"reflect"
)

func main() {
	a0 := new([]int)
	fmt.Println("==========a0 := new([]int)=========")
	fmt.Printf("type:%+v \n", reflect.TypeOf(a0))
	fmt.Printf("len:%+v \n", len(*a0))
	fmt.Printf("cap:%+v \n", cap(*a0))
	fmt.Printf("%+v \n", a0)

	var a1 []int
	fmt.Println("==========var a1 []int=========")
	fmt.Printf("type:%+v \n", reflect.TypeOf(a1))
	fmt.Printf("len:%+v \n", len(a1))
	fmt.Printf("cap:%+v \n", cap(a1))
	fmt.Printf("%+v \n", a1)

	var a2 [10]int
	fmt.Println("==========var a2 [10]int=========")
	fmt.Printf("type:%+v \n", reflect.TypeOf(a2))
	fmt.Printf("len:%+v \n", len(a2))
	fmt.Printf("cap:%+v \n", cap(a2))
	fmt.Printf("%+v \n", a2)

	a3 := make([]int, 10)
	fmt.Println("==========a3 := make([]int, 10)=========")
	fmt.Printf("type:%+v \n", reflect.TypeOf(a3))
	fmt.Printf("len:%+v \n", len(a3))
	fmt.Printf("cap:%+v \n", cap(a3))
	fmt.Printf("%+v \n", a3)

	a4 := make([]int, 10, 50)
	fmt.Println("==========a4 := make([]int, 10, 50)=========")
	fmt.Printf("type:%+v \n", reflect.TypeOf(a4))
	fmt.Printf("len:%+v \n", len(a4))
	fmt.Printf("cap:%+v \n", cap(a4))
	fmt.Printf("%+v \n", a4)
}
```
看一下执行结果
```
$ go run main.go
==========a0 := new([]int)=========
type:   *[]int   
len:    0 
cap:    0 
&[]         //跟上面做的测试一样，零内存，没分配空间。 
==========var a1 []int=========
type:   []int  
len:    0 
cap:    0 
[]          //分了空间，空间长度是0
==========var a2 [10]int=========
type:   [10]int 
len:    10 
cap:    10 
[0 0 0 0 0 0 0 0 0 0]  //分了长度为10的空间，空间里默认存了0
==========a3 := make([]int, 10)=========
type:   []int 
len:    10 
cap:    10 
[0 0 0 0 0 0 0 0 0 0]  //分了长度为10的空间，空间里默认存了0，预留的空间也是10
==========a4 := make([]int, 10, 50)=========
type:   []int 
len:    10 
cap:    50 
[0 0 0 0 0 0 0 0 0 0]  //分了长度为10的空间，空间里默认存了0，预留的空间是50
```

#### 所以make是跟new不一样会分配已初始化的值。

那很容易理解下面的写法虽然程序不会报错，但是不太合适吧？  

```go
var a *[]int = new([]int)
*a = make([]int, 10, 100)
```

#### make的预留空间

make函数有第三个参数，是给创建的类型留给的预留空间。  
没有传这参数是默认是和长度一样，当空间不够的时候后会自动扩容。  
**每次扩容操作会增大1倍。**

可以做一个简单的测试。

```go
package main

import (
	"fmt"
	"reflect"
)

func main() {
	fmt.Println("==========没有赋预留空间==========")

	slice := make([]int, 0)
	fmt.Printf("len :%+v cap:%+v \n", len(slice), cap(slice))

	for i := 0; i < 10; i++ {
		slice = append(slice, i)
		fmt.Printf("len :%+v cap:%+v \n", len(slice), cap(slice))
	}

	fmt.Println("==========赋预留空间==========")
	slice1 := make([]int, 0, 5)
	for i := 0; i < 6; i++ {
		slice1 = append(slice1, i)
		fmt.Printf("len :%+v cap:%+v \n", len(slice1), cap(slice1))
	}
}
```
执行结果如下
```
$ go run main.go
==========没有赋预留空间==========
len :0 cap:0 
len :1 cap:1 
len :2 cap:2 
len :3 cap:4  //自动扩容
len :4 cap:4 
len :5 cap:8  //自动扩容
len :6 cap:8 
len :7 cap:8 
len :8 cap:8 
len :9 cap:16  //自动扩容
len :10 cap:16 
==========赋预留空间==========
len :1 cap:5 
len :2 cap:5 
len :3 cap:5 
len :4 cap:5 
len :5 cap:5 
len :6 cap:10  //自动扩容
```

这些自动扩容机制大部分的开发语言都很相似。  

### 3. 总结

new函数返回的是执行内存的指针。  
new日常开发中使用是比较少的，可以用直接声明的方式代替。  
make函数只支持 slice, map, channel 三种类型。  
make函数分配并初始化类型所需的内存空间和结构，并赋默认值。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`