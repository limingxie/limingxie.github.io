---
title: "Go项目的测试代码1（基础）"
date: 2018-05-27T20:36:02+08:00
draft: true
---

最近写了测试代码，整理了一下。

先看看简单的测试代码

```
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

```
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

```
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
[Go项目的测试代码（项目运用）](https://limingxie.github.io/limingxie/my_go_test1/)

### 未完继续
