---
title: "solid原则的再次整理"
date: 2019-06-06T07:28:49+08:00
draft: true
---

最近对代码结构比较感兴趣。  
使得让我再次关注solid原则。  

```
SRP	    The Single Responsibility Principle	    单一责任原则
OCP	    The Open Closed Principle	            开放封闭原则
LSP	    The Liskov Substitution Principle	    里氏替换原则
ISP	    The Interface Segregation Principle	    接口分离原则
DIP	    The Dependency Inversion Principle	    依赖倒置原则
```


**◆ 单一责任原则(The Single Responsibility Principle)**  
```
一个类只完成它应该完成的职责。
```
一个class，一个library, 一个package ... 只完成自己的单一职责。  
但是也很容易走极端。
比如一个微服务只完成一个功能？  
我也不知道这么做好不好，但是我还是倾向于符合自己的实际情况。  

比如有职员类，有销售登记和配送管理函数。  
这时你可以拆分店员和物流管理人员2个类。  
但是这个类只包含这两个函数，那你何必要分着管呢？


**◆ 开放封闭原则(The Open Closed Principle)**  
```
软件实体(类,模块,函数等等)应当对扩展开放，对修改闭合。
```  
这其实就是要关注，增加新的功能的时候能不能不修改以前旧代码？  
增加新功能，需要大概旧代码，说明没做好开放封闭原则。

**◆ 里氏替换原则(The Liskov Substitution Principle)**  
```
只有在确定是 is-a 的关系时才能使用继承。
```  
其实就是替换别的模块的时候不要修改原有的旧模块。  
遵守 is-a 的继承规则。

**◆ 接口分离原则(The Interface Segregation Principle)**  
```
如果一个接口包含了过多的方法，应该通过分离接口将其拆分。
```  
如果依赖和自己没有关系或不需要的东西，需要应该把他拆分，只依赖和自己有关系的不封。

**◆ 依赖倒置原则(The Dependency Inversion Principle)**  
```
上层(抽象)不应该依赖于下层(实体)，下层(实体)应该依赖于上层(抽象)。
```  
下层依赖上层，上层控制下层。程序的调用应是上层留到下层。
<br />
<br />
##### **感觉还是没有理解透，下次有更深的理解，再次整理我的最爱`solid原则`。**

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`