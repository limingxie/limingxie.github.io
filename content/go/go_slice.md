---
author: "li_mingxie"
title: "【go笔记】 数组, 切片(slice), 映射(map)简介"
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

我们在日常变成中经常用到数组, 切片(slice), 映射(map)。  
这篇文章中简单的介绍一下这3中结构。  

### 1. 数组

数组是我们接触到的最基本的结构，在Go里面的数组有一些比较特别的地方。如下：  

1. 数组是值。将一个数组赋予另一个数组会复制其所有元素。
2. 若将某个数组传入某个函数，它将接收到该数组复制一份而非指针。
3. 数组的大小是其类型的一部分。类型 [10]int 和 [20]int 是不同的。

数组为值的属性很有用，但代价也高，我们也可以传递一个指向该数组的指针。

### 2. 切片(slice)

本篇文章的重点来了。一般情况下，很少直接用数组。  
比如像java 我们经常会用到ArrayList, Go是经常会用到切片(slice)。  
切片(slice)通过对数组进行封装，为数据序列提供了更通用、强大而方便的接口。  

切片的长度可以通过len()获得，容量可通过内建函数cap()获得。  
如果超出长度需要用append函数追加到下一个位置中。  
当超出预留量时，自己会做1倍的扩容。  

切片还有几个明显的特点，我们先看看下面的例子:  

```go
package main

import "fmt"

func main() {

	fmt.Println("==============var声明和make的内存分配==============")
	var a0 []int
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", a0, len(a0), cap(a0), &a0, a0, a0)
	b0 := make([]int, 0)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", b0, len(b0), cap(b0), &b0, b0, b0)

	fmt.Println("==============自动扩容测试==============")
	a := make([]int, 2, 3) //声明一个长度为2，容量为3的slice
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", a, len(a), cap(a), &a, a, a)
	a[1] = 1         //因是长度内的可以直接赋值
	a = append(a, 2) //超出长度只能用append赋值
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", a, len(a), cap(a), &a, a, a)
	a = append(a, 3)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", a, len(a), cap(a), &a, a, a)
	a = append(a, 4)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", a, len(a), cap(a), &a, a, a)

	fmt.Println("==============存储地址的变化==============")
	var b []int
	b = a	//测试给别的变量赋值会有什么变化
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", b, len(b), cap(b), &b, b, b)
	c := a[:2] //测试截取的变化
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", c, len(c), cap(c), &c, c, c)
	d := a[2:] //测试截取的变化
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", d, len(d), cap(d), &d, d, d)
}
```

执行结果如下

```
$ go run main.go
==============var声明和make的内存分配==============
type: []int | len: 0 | cap: 0 | head-address: 0xc00000c048 | value-address: 0x0 | value:[] 		//var 没有分配值内存空间
type: []int | len: 0 | cap: 0 | head-address: 0xc00000c060 | value-address: 0x1190370 | value:[] 	//make是分配了值内存空间
==============自动扩容测试==============
type: []int | len: 2 | cap: 3 | head-address: 0xc00000c0c0 | value-address: 0xc000018108 | value:[0 0] 		//赋了默认值
type: []int | len: 3 | cap: 3 | head-address: 0xc00000c0c0 | value-address: 0xc000018108 | value:[0 1 2] 	//值内存地址没有变
type: []int | len: 4 | cap: 6 | head-address: 0xc00000c0c0 | value-address: 0xc000016180 | value:[0 1 2 3] 	//因超出容量，所以值内存地址有了变化，容量也扩到原来的2倍
type: []int | len: 5 | cap: 6 | head-address: 0xc00000c0c0 | value-address: 0xc000016180 | value:[0 1 2 3 4] 	//值内存地址和容量没什么变化
==============存储地址的变化==============
type: []int | len: 5 | cap: 6 | head-address: 0xc00000c1f8 | value-address: 0xc000016180 | value:[0 1 2 3 4] 	//b=a 因是不同变量，所以头部内存地址有变化，值内存地址没变
type: []int | len: 2 | cap: 6 | head-address: 0xc00000c258 | value-address: 0xc000016180 | value:[0 1]  	//a[:2]截取前面的值，值内存地址没变化，容量也没变化
type: []int | len: 3 | cap: 4 | head-address: 0xc00000c2b8 | value-address: 0xc000016190 | value:[2 3 4] 	//a[2:]截取后面的值，值内存地址有变化，容量被截掉了
```

那切片(slice)在函数里传参时会有什么变化呢？  
我们做一下简单的试验:  

```go
package main

import (
	"fmt"
)

func main() {

	slice1 := make([]int, 2, 4)
	fmt.Println("==============【测试1】进函数之前slice1的信息==============")
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", slice1, len(slice1), cap(slice1), &slice1, slice1, slice1)
	test1(slice1)
	fmt.Println("==============【测试1】出函数后slice1的信息==============")
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice1, len(slice1), cap(slice1), &slice1, slice1, slice1)

	fmt.Println("")
	fmt.Println("")

	slice2 := make([]int, 2, 4)
	fmt.Println("==============【测试2】进函数之前slice2的信息==============")
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", slice2, len(slice2), cap(slice2), &slice2, slice2, slice2)
	test2(&slice2)
	fmt.Println("==============【测试1】出函数后slice2的信息==============")
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice2, len(slice2), cap(slice2), &slice2, slice2, slice2)
}

func test1(slice []int) {
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", slice, len(slice), cap(slice), &slice, slice, slice)

	a := slice
	a[1] = 1
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice, len(slice), cap(slice), &slice, slice, slice)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", a, len(a), cap(a), &a, a, a)

	a = append(a, 2)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice, len(slice), cap(slice), &slice, slice, slice)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", a, len(a), cap(a), &a, a, a)

	slice = append(slice, 6)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice, len(slice), cap(slice), &slice, slice, slice)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", a, len(a), cap(a), &a, a, a)

	a = append(a, 8)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice, len(slice), cap(slice), &slice, slice, slice)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", a, len(a), cap(a), &a, a, a)

}

func test2(slice *[]int) {
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", slice, len(*slice), cap(*slice), slice, *slice, slice)

	a := slice
	(*a)[1] = 2
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice, len(*slice), cap(*slice), slice, *slice, slice)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", a, len(*a), cap(*a), a, *a, a)

	*a = append(*a, 2)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n", slice, len(*slice), cap(*slice), slice, *slice, slice)
	fmt.Printf("type: %T | len: %d | cap: %d | head-address: %p | value-address: %p | value:%+v \n\n", a, len(*a), cap(*a), a, *a, a)
}
```

执行结果如下  

```
$ go run main.go
==============【测试1】进函数之前slice1的信息==============
type: []int | len: 2 | cap: 4 | head-address: 0xc0000a6030 | value-address: 0xc0000b6000 | value:[0 0] 

type: []int | len: 2 | cap: 4 | head-address: 0xc0000a6090 | value-address: 0xc0000b6000 | value:[0 0] 		//进入函数，会取新的头部地址，值的内存地址不变

type: []int | len: 2 | cap: 4 | head-address: 0xc0000a6090 | value-address: 0xc0000b6000 | value:[0 1] 		//新声明变量a的头部地址不一样，值内存地址一样。而且a[1]=1的操作，因值内存地址一样，所以都受到了影响
type: []int | len: 2 | cap: 4 | head-address: 0xc0000a60f0 | value-address: 0xc0000b6000 | value:[0 1] 

type: []int | len: 2 | cap: 4 | head-address: 0xc0000a6090 | value-address: 0xc0000b6000 | value:[0 1] 		//a = append(a, 2) 以后参数slice 没变化，只有a的值有变化。
type: []int | len: 3 | cap: 4 | head-address: 0xc0000a60f0 | value-address: 0xc0000b6000 | value:[0 1 2] 

type: []int | len: 3 | cap: 4 | head-address: 0xc0000a6090 | value-address: 0xc0000b6000 | value:[0 1 6] 	//slice = append(slice, 6) 这里发生了有意思的现象了，6把2取代了。这个地方需要非常注意。
type: []int | len: 3 | cap: 4 | head-address: 0xc0000a60f0 | value-address: 0xc0000b6000 | value:[0 1 6] 

type: []int | len: 3 | cap: 4 | head-address: 0xc0000a6090 | value-address: 0xc0000b6000 | value:[0 1 6] 	//a = append(a, 8) 因两个变量都append到第三个索引，所以下一次的append是不会被覆盖了。
type: []int | len: 4 | cap: 4 | head-address: 0xc0000a60f0 | value-address: 0xc0000b6000 | value:[0 1 6 8] 

==============【测试1】出函数后slice1的信息==============
type: []int | len: 2 | cap: 4 | head-address: 0xc0000a6030 | value-address: 0xc0000b6000 | value:[0 1] 		//出函数只影响到值内存的变化，函数里的append操作没有受到影响


==============【测试2】进函数之前slice2的信息==============
type: []int | len: 2 | cap: 4 | head-address: 0xc0000a6270 | value-address: 0xc0000b6018 | value:[0 0] 

type: *[]int | len: 2 | cap: 4 | head-address: 0xc0000a6270 | value-address: 0xc0000b6018 | value:&[0 0] 	//参数为*slice 进入函数，什么都没变

type: *[]int | len: 2 | cap: 4 | head-address: 0xc0000a6270 | value-address: 0xc0000b6018 | value:&[0 2] 	//两个变量都受到影响
type: *[]int | len: 2 | cap: 4 | head-address: 0xc0000a6270 | value-address: 0xc0000b6018 | value:&[0 2] 

type: *[]int | len: 3 | cap: 4 | head-address: 0xc0000a6270 | value-address: 0xc0000b6018 | value:&[0 2 2] 	//两个变量都受到影响
type: *[]int | len: 3 | cap: 4 | head-address: 0xc0000a6270 | value-address: 0xc0000b6018 | value:&[0 2 2] 

==============【测试1】出函数后slice2的信息==============
type: []int | len: 3 | cap: 4 | head-address: 0xc0000a6270 | value-address: 0xc0000b6018 | value:[0 2 2]	//估计也预想到了，都会受到影响
```

如上测试我们可以总结出几个需要注意的点：  

1. 用make声明的时候，会分配值内存地址和默认值。
2. 超出容量是会自动扩容(原来的2倍)。这时值内存地址会有变化。
3. 当做函数的参数传进去的时候，用的值内存地址是一样，但是会使用新建的头部内存地址。
4. 不同的变量使用同一个值内存地址时需要注意相互受到的影响。

### 3. 映射(map)

如果了解了上述切片(slice)的内容，映射(map)也没什么复杂的。  
它可以关联不同类型的值。其键可以是任何相等性操作符支持的类型，  
如整数、浮点数、复数、字符串、指针、接口（只要其动态类型支持相等性判断）、结构以及数组。 切片不能用作映射键。

我比较喜欢用if _, ok := map[parm]的方式取值。具体如下：  

```go
package main

import "fmt"

var ages = map[string]int{
	"Ann": 19,
	"Joe": 20,
}

func main() {
	printAge("Joe")
	printAge("Jack")
}

func printAge(name string) {

	if age, ok := ages[name]; ok {
		fmt.Printf("%s is %d years old. \n", name, age)
	} else {
		fmt.Printf("%s is unknown.\n", name)
	}
}
```

执行结果如下  

```
$ go run main.go
Joe is 20 years old. 
Jack is unknown.
```

### 总结

需要用key-value结构的话就用映射(map)。  
一般不会用到数组结构，大部分情况下都会用比较方便的切片(slice)。  
使用切片(slice)的时候，需了解容量及自动扩容的机制(会增加1倍的容量，头部地址不变，值的内存地址会有变化)。  
切片(slice)当做参数，或是相互赋值的时候会出现共用值内存地址的情况，这时需要注意一下有没有数据被串改的可能性。  


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`