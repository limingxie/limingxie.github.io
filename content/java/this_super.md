---
title: "【java笔记】this和super简介"
date: 2020-07-26T12:28:49+08:00
draft: true
---

本来是确认构造函数的运行方式的。  
结果看到继承以及super关键字了...^^;;   

都说this是类似于指自己的一个指针，super是更像一个关键字。  
可能我的了解不是那么深入，觉得this就是指自己super就是指父类。其余没感觉到什么特别。  
而且感觉前期了解这些就可以了，后面对Java有更深层次的了解后继续在研究~  
下面是我简单的试一试，做个记录...^^  

> 
1.this指向自己。  
2.super指向父类。  
3.多层继承时，super指向离自己最近的上一层父类。  
4.不能同时继承类和接口，也不能同时继承抽象类和接口。  
5.构造函数中使用this或super时，必须写在第一行。而且不能同时使用。  

我先创建了以下文件。  
[图片备用地址](https://limingxie.github.io/images/java/super/super_test.png)  
![文件目录结构](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/super/super_test.png)  

先看看每个文件的代码  

**【Animal】一个普通的类和函数。**
```java
package supertest.animal;

public class Animal {
    // 姓名属性，和子类的值不一样
    protected String name = "animalName";

    // 无参构造函数
    public Animal() {
        System.out.println("Animal: There are no parameters");
    }

    // 有参构造函数
    public Animal(String str) {
        System.out.println("parameters:" + str);
    }

    // 父类的函数，子类会重写这个函数
    public void run(String str) {
        System.out.println(str+ ":" + Animal.class);
    }
}
```

**【Horse】子类继承了Animal父类**
```java
package supertest.MammalInt;

import supertest.animal.Animal;

public class Horse extends Animal {
    // 姓名属性，和父类的值不一样
    protected String name = "horseName";

    // 无参构造函数
    public Horse() {
        // 这里执行父类有参的构造函数
        super("super has parameters");
        System.out.println("Horse: There are no parameters.");
    }

    // 有参构造函数
    public Horse(String str) {
        System.out.println("parameters:" + str);
    }

    // 查看this和super的name
    public void printName(){
        System.out.println("---------------------this.name------------------------");
        System.out.println("this.name:" + this.name);
        System.out.println("---------------------super.name-----------------------");
        System.out.println("super.name:" + super.name);
    }

    // 重写了父类的run函数
    public void run(String str) {
        System.out.println(str+ ":" + Horse.class);
    }

    // 查看this和super的run函数
    public void testRun(String name) {
        System.out.println("---------------------this.run-------------------------");
        this.run(name);
        System.out.println("---------------------super.run------------------------");
        super.run(name);
    }
}
```

```java
package test.supertest;

import test.supertest.animal.Animal;
import test.supertest.MammalInt.Horse;

public class main {
    public static void main(String[] args) {
        // 先执行父类的构造函数，然后在执行子类的构造函数
        System.out.println("---------------------constructor----------------------");
        Horse horse = new Horse();

        // 查看this和super的name
        horse.printName();

        // 查看this和super的函数
        horse.testRun("testRun");
    }
}
```

**编译以后，可以查看结果**
```
$ javac supertest/animal/Animal.java supertest/MammalInt/Horse.java supertest/main.java

$ java supertest.main
---------------------constructor----------------------
parameters:super has parameters
Horse: There are no parameters.
---------------------this.name------------------------
this.name:horseName
---------------------super.name-----------------------
super.name:animalName
---------------------this.run-------------------------
testRun:class supertest.MammalInt.Horse
---------------------super.run------------------------
testRun:class supertest.animal.Animal
```

做了最最基本的简单测试，别见笑...^^

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`