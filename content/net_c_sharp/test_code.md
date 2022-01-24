---
author: "li_mingxie"
title: "测试代码基本结构"
date:  2020-10-30T08:28:49+08:00
description: ""
tags: [
    "C#",
    "test",
    "arrange",
    "act",
    "assert",
]
categories: [
    "C#",
    "test",
]
---

最近有机会写.net的项目了, 习惯性的研究了如何去写测试代码。
这次看资料比起以前研究golang的测试稍微详细了点，哈~ <!--more-->  

## **测试代码的基本结构**

测试代码是用代码去测试开发的功能，所以目前普遍公认的测试代码模板结构。  
分三个部分：arrange, act, assert。分别起的作用如下。

------

> 1）arrange: 测试之前需要准备的代码。  
> 2）act: 实际要测试的方法。  
> 3）assert: 结果确认。  

-----

看下代码更实在。懒得从新写了。  
把以前的golang代码[Go项目的测试代码1（基础）](https://limingxie.github.io/go/my_go_test/)粘过来了...^^;;

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

这是我们能看到的最简单的测试代码，但是现实和理想还是有差距的。  
当我们进行测试的时候很多时候是和其他模块有依赖或调用的关系的。  
为了解决这些问题我们会使用测试替身(Test Double)。

## **测试替身(Test Double)**

测试替身起源于拍电影时，拍摄一些高难度或是危险动作时会用替身演员。  

Test Double(测试替身) 也是当我们需要用一些功能和依赖项时，可以使用“替身”。  
这样我们可以专注于我们需要测试的代码，而不需要把时间浪费在"周围的关系"中。  

测试替身的类型可以分为 Dummary、Fake、Stub、Spy、Mock。
每个类型我都写了一些简单的代码，易于理解。

[图片备用地址](https://limingxie.github.io/images/c_sharp/test_double.png)  
![test_double](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/c_sharp/test_double.png)

#### Dummy
+ 最简单、最原始的测试替身型别。
+ 需要实体类，但是不需要其功能的时候会用。
+ 调用Dummy类的函数不保障正常的使用。
+ 只传对象，但不使用，通常只是被用来填充参数列表。

简单的说，Dummy是一个执行过程中为需要创建的冒充的对象，不能保障正常的功能。
看看下面的例子：

```java
public interface People {
    void running();
}

public class PeopleDummy implements People {
    @Override
    public void running() {
        // 不采取任何操作
    }
}
```

正常是需要实现running()方法，但是在特定的测试场景中不需要这些操作。  
这时running()方法不会影响测试内容，也没必要去实现。  
像这样不能正常工作也不影响测试，测试时又需要的对象叫做Dummy对象。  

#### Stub
+ Dummy的上一级，可以模拟可执行的Dummy对象。
+ Stub 是接口或基类的最低限度的实现。
+ 针对测试时的请求只返回约定好的结果值。  

简单的说，Stub起到的作用是只返回约定好的结果值。
看看下面的例子：

```java
public class StubUser implements User {
    // ...
    @Override
    public User findById(long id) {
        return new User(id, "Test User");
    }
}
```

看上述代码调用StubUser类的findById方法时，返回接受的id和name是Test User的实体对象。  
像这样只为测试返回特定值得对象叫做stub。

#### Spy
+ Stub的上一级，比起stub多记录自身被调用的情况。
+ 测试时记录自身对象是否被调用，举例子的话邮件服务会记录发送了多少封邮件。  
+ 还会记录调用了哪些成员，好让单元测试验证所调用的成员是否符合预期。

简单的说，Spy是多特定函数是否正常的调用的记录。  
顺便也可以当做Stub使用。
看看下面的例子：

```java
public class MailService {
    private int sendMailCount = 0;
    private Collection<Mail> mails = new ArrayList<>();

    public void sendMail(Mail mail) {
        sendMailCount++;
        mails.add(mail);
    }

    public long getSendMailCount() {
        return sendMailCount;
    }
}
```

这里调用MailService的sendMail方法时，会记录发送的次数。  
后续可以通过getSendMailCount()获取发邮件的次数。  
像这样存储调用记录的类叫做Spy。

#### Fake
+ 则具有可以正常工作的实现，不是完整的生产对象，是把复杂的内容简化后的对象。
+ 通常采用了一些不适合生产环境的便捷手段。（一个典型例子是内存数据库）。

简单的说Fake是模拟了和生产一样的对象，但是其内容是简化的。
看看下面的例子：

```java
public class User {
    private Long id;
    private String name;
    
    protected User() {}
    
    public User(Long id, String name) {
        this.id = id;
        this.name = name;
    }
    
    public Long getId() {
        return this.id;
    }
    
    public String getName() {
        return this.name;
    }
}

public interface IUserService {
    void save(User user);
    User findById(long id);
}

public class FakeUserService implements IUserService {
    private Collection<User> users = new ArrayList<>();
    
    @Override
    public void save(User user) {
        if (findById(user.getId()) == null) {
            user.add(user);
        }
    }
    
    @Override
    public User findById(long id) {
        for (User user : users) {
            if (user.getId() == id) {
                return user;
            }
        }
        return null;
    }
}
```
上面是一个很简单的使用Fack对象的例子。  
用实际UserService里的保存和查询方法需要连接数据库。  
在测试环境下，不影响主要测试内容的前提下，  
可以使用Fack类来取代实际连数据库的操作。  
像这样使用摸你的虚假类叫Fack。

#### Mock
就是我们这里所谈论的：它根据预先编写的逻辑，基于调用者所期望的返回值。
Mock 是由 Mock 链接库动态建立 (其他通常是由测试开发人员使用程序代码来产生)。  
测试开发人员永远看不到实作接口或基类的实际程序代码，但是可以设定 Mock 以提供传回值、预期要叫用的特定成员...等等。  
视其中的设定而定，Mock 的行为可能会像 Dummy、Stub 或 Spy。

Mocks预先编程了期望，这些期望形成了他们期望接收的调用的规范。如果他们收到一个他们不期望的呼叫，并且在验证过程中被检查以确保他们收到了他们期望的所有呼叫，他们可以抛出异常。

mock对象是指对外面依赖系统的模拟，在运行时刻可以根据假设的需求提供期望的结果。
fake对象是一种虚假的实现，内部使用了固定的数据或逻辑，只能返回特定的结果。



Mock
Mocks are objects that register calls they receive. In test assertion we can verify on Mocks that all expected actions were performed.Mocks 
代指那些仅记录它们的调用信息的对象，在测试断言中我们需要验证 Mocks 被进行了符合期望的调用。
当我们并不希望真的调用生产环境下的代码或者在测试中难于验证真实代码执行效果的时候，我们会用 Mock 来替代那些真实的对象。典型的例子即是对邮件发送服务的测试，我们并不希望每次进行测试的时候都发送一封邮件，毕竟我们很难去验证邮件是否真的被发出了或者被接收了。我们更多地关注于邮件服务是否按照我们的预期在合适的业务流中被调用，其概念如下图所示：

Mock是行为的验证，Stub是对状态的验证

#### 总结
Dummy是一个空壳。  
Stub是给这个Dummy空壳加了些线路让他能模拟运行。  
Spy是给这个Stub加了存储器，让他有些记忆。  
到了Fake和Mock就是比较完整的替身了。  
那Fake和Mock作用是什么？  
Fack是一种实体的模拟(虚假实例)，而Mock是对行为逻辑的模拟(虚假行为)。  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`