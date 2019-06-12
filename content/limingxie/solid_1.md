---
title: "设计原则那些事儿"
date: 2019-06-06T07:28:49+08:00
draft: true
---

我们写代码的时候，尽量去遵守这些原则，对项目架构会有很多帮助。  
但是这些乱七八糟的原则未免也太多了...^^;;  
我了方便去理解和记住这些原则，  
简单的整理了常见的，公认的几个原则。  
(即使这么写，我也没记清这些原则... 哎~ )  

代码架构的设计原则中最普遍的是solid原则。  

```
SRP	    Single Responsibility Principle	    单一责任原则
OCP	    Open Closed Principle	            开放封闭原则
LSP	    Liskov Substitution Principle	    里氏替换原则
ISP	    Interface Segregation Principle	    接口分离原则
DIP	    Dependency Inversion Principle	    依赖反转原则
```


**◆ SRP:单一责任原则(Single Responsibility Principle)**  
```
一个类只完成它应该完成的职责。
```
任何一个软件模块都应该只对某一类行为者负责。
它的作用就是告诉我们在哪里划清边界。

**◆ OCP:开放封闭原则(Open Closed Principle)**  
```
软件实体(类,模块,函数等等)应当对扩展开放，对修改闭合。
```  
这其实就是要关注，增加新的功能的时候能不能不修改以前旧代码？  
增加新功能，需要大改旧代码，说明没做好开放封闭原则。

**◆ LSP:里氏替换原则(Liskov Substitution Principle)**  
```
只有在确定是 is-a 的关系时才能使用继承。
```  
其实就是替换别的模块的时候不要修改原有的旧模块。  
遵守 is-a 的继承规则。

**◆ ISP:接口分离原则(Interface Segregation Principle)**  
```
如果一个接口包含了过多的方法，应该通过分离接口将其拆分。
```  
如果依赖和自己没有关系或不需要的东西，需要应该把他拆分，只依赖和自己有关系的不分。

**◆ DIP:依赖反转原则(Dependency Inversion Principle)**  
```
上层(抽象)不应该依赖于下层(实体)，下层(实体)应该依赖于上层(抽象)。
```  
换句话说上层策略性代码不应该依赖底层细节性代码，  
底层细节性代码应该依赖上层策略性的代码。

<br />
<br />
##### **以下是**

**◆ REP:复用/发布等同原则(Release Reuse Equivalency Principle)**  
```
待整理...
```  
待整理...

**◆ CCP:共同闭包原则(Common Closure Principle)**  
```
待整理...
```  
待整理...

**◆ CRP:共同复用原则(Composite Reuse Principle)**  
```
待整理...
```  
待整理...

**◆ ADP:无依赖环原则(Acyclic Dependencies Principle)**  
```
待整理...
```  
待整理...

**◆ SDP:稳定依赖原则(Stable Dependencies Principle)**  
```
待整理...
```  
待整理...

**◆ SAP:稳定抽象原则(Stable Dependencies Principle)**  
```
待整理...
```  
待整理...

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`