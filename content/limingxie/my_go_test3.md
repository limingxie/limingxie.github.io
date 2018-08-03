---
title: "Go项目的测试代码3（测试替身Test Double）"
date: 2018-05-27T20:38:02+08:00
draft: true
---

上一篇文章介绍了项目中测试代码的写法。   
[Go项目的测试代码2（项目运用）](https://limingxie.github.io/limingxie/my_go_test1/)
   
  
这里简单的共享一下测试替身。

当我们写测试代码的时候，经常遇到一个问题。  
跟别的模块或服务`有依赖性`，可是功能还没开发完。  
或是因为各种原因(安全，白名单，内网等等...)开发环境里无法调用相应的模块。  
这时候我们应该如何去写测试代码？

因此`测试替身Test Double`出现了。
其功能和名字一样，做替身。

测试替身Test Double的分以下类型。 
Dummy, Stub, Fake, Spy, Mock

## Dummy
最简单、最原始的测试替身型别。Dummy 没有实作，最常用于需要参数值但不使用它的情况。  
Null 可视为是 Dummy，但真的 Dummy 是接口或基类的衍生，且完全不包含实作。

## Stub
Dummy 的上一级，Stub 是接口或基类的最低限度实作。  
会传回 Void 的方法通常完全不包含实作，而会传回值的方法通常会传回硬式编码的值。

## Spy
测试 Spy 类似 Stub，但除了提供客户端可叫用成员的实例，  
Spy 还会记录叫用了哪些成员，好让单元测试验证所叫用的成员是否符合预期。

## Fake
Fake 包含更复杂的实作，通常涉及所继承型别之不同成员之间的互动。  
虽然不是完整的生产实作，但 Fake 与生产实作很相似，尽管它会采取一些快捷方式。

## Mock
Mock 是由 Mock 链接库动态建立 (其他通常是由测试开发人员使用程序代码来产生)。  
测试开发人员永远看不到实作接口或基类的实际程序代码，但是可以设定 Mock 以提供传回值、预期要叫用的特定成员...等等。  
视其中的设定而定，Mock 的行为可能会像 Dummy、Stub 或 Spy。

-----

**`上面是网上流行的概念和理论。`**


#  我在项目中的运用

我开发的是在企业微信中的应用，所以需要调用企业微信的API，  
用过的人应该知道，这需要登记可信域名(简单的说`本地不能调用`，只能发布到服务器才能调用)。  
也可以用第三方工具去模拟调试，但是这么做感觉很不方便。而且Debug也相当麻烦。

我是这么处理的。

#### **项目初期(一阶段)**

直接写`硬代码hardcode`。

一般我们都会说尽量避免写硬代码hardcode,   
但是`项目初期我建议大家这么写`。  
为什么？ 其实很简单。  
初期我们应该把所有的精力放在`自己模块的功能和稳定性`。  
没必要在别人的接口里耗精力, 甚至去考虑怎么和其他模块做联动。  

例子：
```
var key string
if config.AppEnv == "test" || config.AppEnv == "dev" { //测试或是开发环境的时候直接赋值
	key = "23836728366"
} else {
	key, err = (Login{}).GetKeyForLogin(ctx, code) //实际调用的接口写在这里
	if err != nil {
		return nil, err
	}
}
```

#### **项目中期(二阶段)**

硬代码hardcode`改写函数function`。

可以写根据参数或设定返回不同值的方法。满足不同场景下有不同返回值的需求。

例子：
```
var key string
if config.AppEnv == "test" || config.AppEnv == "dev" { 
	key = GetKeyForLoginForTest(ctx, code) //测试或是开发环境的时候直接赋值
} else {
	key, err = (Login{}).GetKeyForLogin(ctx, code) //实际调用的接口写在这里
	if err != nil {
		return nil, err
	}
}
```
```
func GetKeyForLoginForTest(ctx context.Context, code string) string {
	var key string
	switch code {
	case "code1":
		key = "key1"
	case "code2":
		key = "key2"
	case :
		......
	default:
		key = "key3"
	}
	return key
}
```

#### **项目后期(三阶段)**

函数function升`类或package`。

这个阶段是可选项，因为这里开始需要耗精力去管理自己的“替身”了。  
根据自己的实际情况来决定要不要这么做。

例子：
```
type LoginTest interface {
	GetKeyForLoginForTest(ctx context.Context, code string) string
}

type SellerTest struct {
}

func (SellerTest) GetKeyForLoginForTest(ctx context.Context, code string) string {
	//销售员业务处理...
	return "SellerKey"
}

type ManagerTest struct {
}

func (ManagerTest) GetKeyForLoginForTest(ctx context.Context, code string) string {
	//业务处理...
	return "ManagerKey"
}

func BuildManagedObject(ctx context.Context, condition string) string {
	switch condition {
	case "condition1":
		return SellerTest{}
	case "condition2":
		return ManagerTest{}
	case :
		......
	default:
		return SellerTest{}
	}
}
```

```
var key string
if config.AppEnv == "test" || config.AppEnv == "dev" { 
	var loginTest LoginTest
	loginTest = BuildManagedObject(condition)
	key = loginTest.GetKeyForLoginForTest(ctx, code) //测试或是开发环境的时候直接赋值
} else {
	key, err = (Login{}).GetKeyForLogin(ctx, code) //实际调用的接口写在这里
	if err != nil {
		return nil, err
	}
}
```
-----

这么做其实能解决大部分的问题。  
不知道我做的项目小或范围小，总之还没有碰到过用以上方式不能解决的问题。  
`有什么不太恰当的部分随时欢迎指正。`

-----

组员不愿意写测试代码而找借口的时候你可以这么说了...^^

> 组员：这次项目依赖性很高, 其他模块的功能还没完善, 开发都很麻烦别说写测试代码了。    
> 我：`你用过测试替身吗？可以很简单的解决这些问题。`

