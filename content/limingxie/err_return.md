---
title: "Go项目:error错误处理"
date: 2019-02-12T08:38:02+08:00
draft: true
---

上一篇文章我介绍了拆分服务后如何去写测试代码的。   
[Go项目:傻瓜式联合测试](https://limingxie.github.io/limingxie/my_go_test4/)  
今天想共享一下，我是如何处理error的。

其实error是一个普通的处理。  
但是你拆分服务后，一个功能调用多个api来实现的时候，错误处理是一件麻烦的事情。

```
有A,B,C 3个服务。  
A服务提供的一个工能是需要 B和C 服务的信息。  
该功能执行的时候报错，单凭返回的错误信息，你是很难一眼看出问题点是在哪里。  
(go 的错误提示过于简单...^^;;)
```

所以我自己写了处理error的一个package。  
https://github.com/limingxie/taoyuan-kit/blob/master/errs/errs.go
<Br />
<Br />

#### **实际运用例子**
这是普通的处理方式
```
package main

import (
	"fmt"
	"strconv"
)

func main() {

	value, err := returnValue()
	if err != nil {
        //普通的处理
		fmt.Println(err)
	} else {
		fmt.Println(value)
	}

}
func returnValue() (int, error) {
	value, err := strconv.Atoi("string")
	if err != nil {
        //普通的处理
		return 0, err
	}
	return value, nil
}
```

执行结果

```
$ go run main.go
strconv.Atoi: parsing "string": invalid syntax
```
------------
返回err的时候套用我的errs.Trace()函数

```
package main

import (
	"fmt"
	"strconv"
	"testCode/errTest/errs"
)

func main() {

	value, err := returnValue()
	if err != nil {
        //套用errs.Trace()处理
		fmt.Println(errs.Trace(err))
	} else {
		fmt.Println(value)
	}

}
func returnValue() (int, error) {
	value, err := strconv.Atoi("string")
	if err != nil {
        //套用errs.Trace()处理
		return 0, errs.Trace(err)
	}
	return value, nil
}
```

其结果是这样的
```
$ go run main.go
strconv.Atoi: parsing "string": invalid syntax
testCode/errTest/main.go:22: 
testCode/errTest/main.go:13: 
```
---------------------

能感受的不同之处了吧？  
之后api的返回err的时候`前面加个`**`服务前缀`**，我就可以知道是哪个服务第几行出问题了。

---------------------

#### **运营的时候怎么办？**

再怎么样的菜鸟也知道，运营服务器出问题直接爆出这些代码信息是不行的。  

那怎么办？  
其实很简单，大家都会留日志吧？只要把这些信息放到日志里就行了， 

前面只显示request_id, 
只要有了request_id 我们就通过这个查找这些错误信息了。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
