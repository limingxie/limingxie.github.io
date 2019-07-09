---
title: "设计原则那些事儿"
date: 2019-06-06T07:28:49+08:00
draft: true
---

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
模块之间应能自由替换。
```  
其实就是替换别的模块的时候不应该影响别的模块，导致发生修改事项。  

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
##### **包的内聚性三原則**

**◆ REP:复用/发布等同原则(Release Reuse Equivalency Principle)**  
```
复用的粒度就是发布的粒度，重用的包需要版本管理，使新版本发布后还可以继续使用老版本。
```  
不应该通过复制粘贴代码来复用，应该通过能发布的包复用代码。

**◆ CCP:共同闭包原则(Common Closure Principle)**  
```
将那些因相同原因同时发生变化的类集合到组件中。
```  
有相同改变原因放入同一個组件。
告诉我们哪些应该放在一起，倾向于把组件个体变大。

**◆ CRP:共同复用原则(Composite Reuse Principle)**  
```
不要强迫依赖他们不需要的东西，
一个包中的所有类应该是共同重用的，如果重用了包中的一个类，就应该重用包中的所有类。
```  
为了不必要的发布，切分没有关联的功能。
告诉我们哪些不一应该放在一起，倾向于把组件个体变小。

##### **包的耦合性三原则**

**◆ ADP:无依赖环原则(Acyclic Dependencies Principle)**  
```
组件或包的依赖关系图中不允许存在环。
```  
不要出现以下情况 A -> B -> C -> A

**◆ SDP:稳定依赖原则(Stable Dependencies Principle)**  
```
依赖关系必须指向更稳定的方向
```  
不稳定的依赖稳定的，稳定的不要依赖不稳定的。

**◆ SAP:稳定抽象原则(Stable Dependencies Principle)**  
```
组件或包的抽象程度应该和其稳定程度一致
```  
越抽象的应该是越稳定的。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`