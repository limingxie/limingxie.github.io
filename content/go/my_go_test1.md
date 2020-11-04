---
author: "li_mingxie"
title: "Go项目的测试代码2（项目运用）"
date: 2018-05-27T20:37:02+08:00
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

上一篇文章介绍了最基本的测试代码的写法。   
[Go项目的测试代码（基础）](https://limingxie.github.io/go/my_go_test/)
   
  
这里简单的共享一下我在项目中使用的方式。<!--more-->

## 项目结构
我们实际项目中, 结构简单地分了`controller`和`model` 

> 因为现在都已微服务的形式开发，没必要太复杂的结构。  
> 分controller和model已经能`满足我们的需求`，`不需要再细分了`。

```
|___config          ||==> 配置文件
| |___config.qa.go
| |___config.production.go
... ...
|___controllers     ||==> controller，只做参数的有效性和简单的逻辑处理
| |___app_api_test.go
| |___app_api.go
| |___init_test.go
| |___tenant_api.go
... ...
|___models          ||==> model，所有的业务都是在这里实现的
| |___app.go
| |___app_test.go
| |___init_test.go
| |___tenant.go
... ...
|___main.go         ||==> 系统的入口
|___factory         |||
|___kit             ||| ==> 按需求自定义建立一个package方便使用
|___filters         |||
... ...
```
> 看项目结构的话，可以发现测试方法直接写在对应的package里面。  
> 有些人喜欢把测试方法分另一个package里写，也可以~ 不同人有不同的喜好。
> 我也试过这种方式，但是还是喜欢写在`同一个package里面`，这么写简单方便。

-----

## controllers和models的不同方式

**controllers**  
controller是提供接口api的直接入口，所以那些重要的api都需要写测试方法。  
我喜欢在这里写`参数验证`，`业务验证`等等`主要功能的测试`。

**models**  
因为controllers里已经做了业务验证了，所以再次做业务验证感觉是多余的。  
我喜欢在这里写方法的`逻辑测试`，一般和数据没太多关系。  
或很`复杂的业务处理`的话也会在这里写测试，和controller做`双重确认`。   

-----

## controllers的测试代码

### init() 函数
每个package里有 init_test.go文件  
这里写 init()方法 执行 go test的时候的`初始化方法`。

```
package controllers

var (
	appEnv           = flag.String("app-env", os.Getenv("APP_ENV"), "app env")
	ctx              context.Context
	echoApp          *echo.Echo
	xormEngine       *xorm.Engine
)

func init() {
	//执行1个线程
	runtime.GOMAXPROCS(1)
	var err error
	//测试数据的链接是内存里的sqlite ==> 每次执行测试代码的时候用到的是内存的sqlite 所以不用管理数据库
	xormEngine, err = xorm.NewEngine("sqlite3", ":memory:")
	if err != nil {
		panic(err)
	}
	//读取配置
	var c configutil.Config
	if err := configutil.Read("", &c, configutil.TestMode); err != nil {
		panic(err)
	}

	//建立Redis链接 没有用到redis可以跳过
	models.SetRedisConn(os.Getenv("TEST_SERVICE_REDIS_CONNECTION"))
	//登记struct到xormEngine
	models.SetXormEngineSync(xormEngine)
	//初始化数据
	models.Seed(xormEngine, configutil.TestMode)

	//echo架构
	echoApp = echo.New()
	echoApp.Validator = &filters.Validator{}

	//为了测试先模拟一个context
	ctx = context.WithValue(context.Background(), echomiddleware.ContextDBName, xormEngine.NewSession())
}
```

### Seed() 函数
我觉得写测试代码的时候，制作`测试数据`的时间比会`占50%以上`。  
让人最头疼的事情。

刚开始的时候我觉得可以直接写。  
如下:
```
var (
	tenants = []Tenant{
		{Id: 1, Code: "eland", Name: "上海衣恋", Enable: true},
		{Id: 1, Code: "T02", Name: "Ice cream", Enable: true},
	}

	brands = []Brand{
		{Id: 1, Code: "EE", Name: "Eland", Enable: true},
		{Id: 2, Code: "EK", Name: "Eland Accessory", Enable: true},
		{Id: 3, Code: "IC", Name: "Haagen-Dazs", Enable: true},
	}
    ... ...
)
func Seed(xormEngine *xorm.Engine) {
	for _, u := range tenants {
		xormEngine.Insert(&u)
	}
	for _, u := range brands {
		xormEngine.Insert(&u)
	}
    ... ...
}
```
但是当有一些`mapping表`的话这么加数据会是个噩梦...ㅠㅠ

这时候可以先把数据添加到`mysql数据库`(使用流行的工具应该还不错吧？)。  
`sqlite`的可视化工具太难用果断放弃。

```
func Seed(xormEngine *xorm.Engine, executeMode configutil.ExecuteMode) {
	//建立原始数据的连接
	driverName := os.Getenv("TEST_SERVICE_DRIVER")
	dataSourceName := os.Getenv("TEST_SERVICE_CONNECTION")
	testXormEngine, err := xorm.NewEngine(driverName, dataSourceName)
	if err != nil {
		panic(err)
	}

	//登记struct
	SetXormEngineSync(testXormEngine)

	//数据保存到内存的sqlite
	Tenant_SetTestData(testXormEngine, xormEngine)
	Brand_SetTestData(testXormEngine, xormEngine)
    ... ...
}

func SetXormEngineSync(xormEngine *xorm.Engine) {
	xormEngine.Sync(new(Tenant))
	xormEngine.Sync(new(Brand))
    ... ...
}

func Tenant_SetTestData(testXormEngine *xorm.Engine, xormEngine *xorm.Engine) {
	var tenants []Tenant
	if err := testXormEngine.Find(&tenants); err != nil {
		fmt.Println(err)
	}
	for _, u := range tenants {
		xormEngine.Insert(&u)
	}
}
... ...

```

## 多人做的项目如何管理原始数据？
我是这么做的  

- 准备一个`公用的mysql数据库`(没有的话可以让下班最晚的员工开数据库共享...^^)  
- 在公共的数据库里`添加原始数据 ` 
- 每个组员都可以`灵活利用Seed方法`，把数据导入到自己本地数据库。  
不嫌慢的话直接调用公用数据库。(也可以[使用docker镜像](https://limingxie.github.io/go/docker_mysql/))  
(Seed方法的原始数据库地址和目标数据库地址可以灵活地去修改，导数据非常easy)  
- 如果原始数据有变化，组员之间`相互共享`，从新导入。


## 接口api测试代码
繁琐的准备工作已经做完，终于可以写测试代码了...^^

```
func Test_ColleagueApiController_GetColleagues(t *testing.T) {
	//需要测试的api
	req := httptest.NewRequest(echo.GET, "/api/v1/colleagues/:id", nil)

	//添加token和context
	c, rec := SetContextWithToken(req)

	//执行完测试代码后需要回滚
	defer factory.DB(c.Request().Context()).Close()
	defer factory.DB(c.Request().Context()).Rollback()
	factory.DB(c.Request().Context()).Begin()

	//参数设定
	c.SetParamNames("id")
	c.SetParamValues("1")

	//调用api方法
	test.Ok(t, ColleagueApiController{}.GetBrandShops(c))
	test.Equals(t, http.StatusOK, rec.Code)

	//api返回结果的结构
	var v struct {
		Result struct {
			ColleagueId int64  `json:"colleagueId"`
			ColleagueNo string `json:"colleagueNo"`
			Name        string `json:"name"`
		} `json:"result"`
		Success bool `json:"success"`
	}

	//验证结果
	test.Ok(t, json.Unmarshal(rec.Body.Bytes(), &v))
	test.Equals(t, v.Result.ColleagueId, int64(1))
	test.Equals(t, v.Result.ColleagueNo, "C000001")
	test.Equals(t, v.Result.Name, "测试人员")
}
```

-----

## models的测试代码

### init() 函数
和controllers 的init()方法没什么区别

```
var (
	appEnv     = flag.String("app-env", os.Getenv("APP_ENV"), "app env")
	xormEngine *xorm.Engine
)

func init() {
	runtime.GOMAXPROCS(1)
	var err error
	xormEngine, err = xorm.NewEngine("sqlite3", ":memory:")
	if err != nil {
		panic(err)
	}
	var c configutil.Config
	if err := configutil.Read("", &c, configutil.TestMode); err != nil {
		panic(err)
	}
	SetModelConfig(&ModelConfig{ValidTimeout: c.ValidTimeout, AppEnv: *appEnv})
	SetRedisConn(os.Getenv("TEST_SERVICE_REDIS_CONNECTION"))
	SetXormEngineSync(xormEngine)

	Seed(xormEngine, configutil.TestMode)
}
```
多写了一个`GetContext()方法`
如果需要数据连接先调用。
```
func GetContextForTest() context.Context {
	return context.WithValue(context.Background(), echomiddleware.ContextDBName, xormEngine.NewSession())
}
```
### 测试代码
models的测试代码如下：
```
func Test_GetTenantAppInfosFromAppContainers(t *testing.T) {
	// 因为这个方法不需要连数据库所以也不需要这些操作
	// ctx := GetContextForTest()
	// factory.DB(ctx).Begin()
	// defer factory.DB(ctx).Close()
	// defer factory.DB(ctx).Rollback()

	appContainers := []TenantAppContainer{
		{TenantAppId: 1, RoleCode: "admin", RoleName: "admin"},
		{TenantAppId: 1, RoleCode: "dev", RoleName: "dev"},
		{TenantAppId: 1, RoleCode: "plan", RoleName: "plan"},
		{TenantAppId: 2, RoleCode: "admin", RoleName: "admin"},
		{TenantAppId: 2, RoleCode: "plan", RoleName: "plan"},
		{TenantAppId: 3, RoleCode: "dev", RoleName: "dev"},
	}

	//测试该方法的功能
	appInfos := GetTenantAppInfosFromAppContainers(appContainers)

	test.Equals(t, len(appInfos), 3)
	test.Equals(t, len(appInfos[0].Roles), 3)
	test.Equals(t, len(appInfos[1].Roles), 2)
	test.Equals(t, len(appInfos[2].Roles), 1)
	test.Equals(t, appInfos[0].Roles[0].RoleCode, "admin")
	test.Equals(t, appInfos[0].Roles[0].RoleName, "admin")
	test.Equals(t, appInfos[0].Roles[2].RoleCode, "plan")
	test.Equals(t, appInfos[0].Roles[2].RoleName, "plan")
	test.Equals(t, appInfos[1].TenantAppId, int64(2))
}
```
-----

你们准备好应对如下的场景吗？

> 产品经理：因为客户的需求我们需要赶紧发布最新版本。    
> 我：稍等，让我确认一下~
```
cd models 
go test
cd ..
cd controllers
go test
```
> 我：`没问题，现在可以发布...^^`

-----

下篇文章介绍项目中我是怎么使用测试替身的...^^  
[Go项目的测试代码3（测试替身Test Double）](https://limingxie.github.io/go/my_go_test3/)

### 未完继续

-------------------------------------------

