---
author: "li_mingxie"
title: "Go项目的测试代码1（基础）"
date: 2018-05-27T20:36:02+08:00
tags: [
    "go",
    "test",
    "golang",
]
categories: [
    "Go",
    "golang",
]
---

最近写了测试代码，整理了一下。<!--more-->

先看看简单的测试代码

```go
// add_test.go  ==> 文件名 _test.go 结尾的默认为测试代码文件
package models

import (
	"testing"
)

//Test开头的默认为测试方法
func TestAdd(t *testing.T) {
	//arrange
	var x, y, res int
	x = 2
	y = 3

	//act
	result = Add(x, y)

	//assert
	if result != 5 {
		t.Fatal("Add的结果不正确")
	}
}
```

```go
// add.go
package models

func Add(x, y int) int {
	return x + y
}
``` 

查看测试结构。

```
$ go test -v    ==> 依次运行当前package的 _test.go文件的测试方法。
=== RUN   TestAdd
--- PASS: TestAdd (0.00s)
PASS
ok      gotest/test5    0.006s
```

------
目前编写测试代码普遍公认的模板结构。

> 1）arrange: 测试之前需要准备的代码。  
> 2）act: 实际要测试的方法。  
> 3）assert: 结果确认。  

-----

**如果测试不通过呢？**

```go
//assert
if result != 6 { //==>结果改成 6
	t.Fatal("Add的结果不正确")
}
```

```
$ go test
--- FAIL: TestAdd (0.00s)
        add_test.go:36: Add的结果不正确
FAIL
exit status 1
FAIL    gotest/test5/models     0.006s
```
<br />
**也可以写一个公用的比较方法, 自定义输出结果。**
```
package test

import (
	"reflect"
	"testing"
)

func Equal(t *testing.T, exp, got interface{}) {
	if !reflect.DeepEqual(exp, got) {
		t.Errorf("\033[31m\n exp: %v \n got: %v \033[39m\n", exp, got)
		t.FailNow()
	}
}
```

```
//assert
if result != 6 { //==>结果改成 6
	t.Fatal("Add的结果不正确")
}

//=====>> 比较的方法改用自己写的test包的Equal方法。

//assert
test.Equal(t, result, 6)
```

输出测试未通过的代码位置，以及比较结果。
```
$ go test
--- FAIL: TestAdd (0.00s)
        test.go:10:
             `exp: 5`
             `got: 6`
FAIL
exit status 1
FAIL    gotest/test5/models     0.007s
```

这样可以按照自己的喜好来自定义提示了。

-----
覆盖率  
**go test -cover**  
```
go test -cover

go test -cover -coverprofile=coverage.out
go tool cover -html=./coverage.out
```

可以查看测试代码的覆盖率，这只是一个补助的功能。  
不需要过分的要求高覆盖率，因为维护测试代码也是耗精力的。

-----

下篇文章介绍项目中我是怎么写测试代码的...^^  
[Go项目的测试代码（项目运用）](https://limingxie.github.io/go/my_go_test1/)

### 未完继续


-------------------------------------

<!-- ## 한국어버전


요즘 테스트코드에 관심을 갖게 되여서 지금 개발하고 있는 Go프로젝트에 적용하여 보았습니다.  
보편화되고 있는 테스트코드는 아래와 같은 내역을 포함하고 있습니다.

------

> 1）arrange: 테스트하기전에 준비해야할 내역.  
> 2）act: 실제 테스트하려고 하는 함수.  
> 3）assert: 결과확인.  

------

간단한 쌤플을 볼까요?
```
// add_test.go  ==> 파일명이 _test.go 로 끄나면 Go에서는 테스트코드 파일이라고 판단합니다.
package models

import (
	"testing"
)

//Test로 시작되는 함수는 테스트함수라고 판단합니다.
func TestAdd(t *testing.T) {
	//arrange
	var x, y, res int
	x = 2
	y = 3

	//act
	result = Add(x, y)

	//assert
	if result != 5 {
		t.Fatal("결과값이 정확하지 않습니다.")
	}
}
```

```
// add.go
package models

func Add(x, y int) int {
	return x + y
}
``` 

위내용을 실행시키면 아래와 같은 결과를 얻을수 있습니다.

```
$ go test -v    ==> 순서대로 해당 package의 _test.go파일에 테스트함수를 실행합니다.
=== RUN   TestAdd
--- PASS: TestAdd (0.00s)
PASS
ok      gotest/test    0.006s
```

-----

**테스트에 실패하면 어떻게 될까요？**

```
//assert
if result != 6 { //==>결과가 6 라고 판단하시면
	t.Fatal("결과값이 정확하지 않습니다.")
}
```

```
$ go test
--- FAIL: TestAdd (0.00s)
        add_test.go:36: 결과값이 정확하지 않습니다.
FAIL
exit status 1
FAIL    gotest/test/models     0.006s
```
<br />

**아래 test라는 패키지를 작성하고 그걸 응용하여 보겠습니다.**

```
package test

import (
	"reflect"
	"testing"
)

func Equal(t *testing.T, exp, got interface{}) {
	if !reflect.DeepEqual(exp, got) {
		t.Errorf("\033[31m\n exp: %v \n got: %v \033[39m\n", exp, got)
		t.FailNow()
	}
}
```

```
//assert
if result != 6 { //==>결과가 6 라고 판단하시면
	t.Fatal("결과값이 정확하지 않습니다.")
}

//=====>> 확인시 새로 작성한test패키지의Equal함수를 사용하면

//assert
test.Equal(t, result, 6)
```
테스트 정화히 어디에서 실해하였는지가 나와서 훨씬 편할겁니다.
```
$ go test
--- FAIL: TestAdd (0.00s)
        test.go:10:
             `exp: 5`
             `got: 6`
FAIL
exit status 1
FAIL    gotest/test5/models     0.007s
```

위의 내용은 자신이 편하신대로 수정하여서 사용하면 됩니다...^^

-----
Coverage  
**go test -cover**  
```
go test -cover

go test -cover -coverprofile=coverage.out
go tool cover -html=./coverage.out
```

이렇게 보면 테스트코드의 Coverage를 알수 있습니다.  
이건 보조기능일뿐 너무 여기 수치에 집착하시면 프로젝트초기엔 힘들어질겁니다.  
먼저 핵심기능에 테스트코드 적용하시고 안정단계에 들어서면 다시 Coverage를 높이게하여도 늦을거 같지 않습니다.


-----

### 미완결(계속)

----------------------------------------------
소중한 의견부탁드립니다. -->

`email: li_mingxie@163.com`
