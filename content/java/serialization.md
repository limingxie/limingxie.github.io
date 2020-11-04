---
author: "li_mingxie"
title: "【java笔记】序列化和反序列化"
date: 2020-07-02T07:28:49+08:00
tags: [
    "java",
    "serializable",
    "序列化",
    "反序列化",
]
categories: [
    "java"
]
---

当做笔记简单了记录了一下。<!--more-->

### **1. 序列化和反序列化是什么？**

**序列化(serializable):** 

    把对象转化为可传输的字节序列过程称为序列化。 

**反序列化(deserialization):** 

    把字节序列还原为对象的过程称为反序列化。  

我觉得网上的这个比喻做的很恰当  

>
如果我们要把一栋房子从一个地方运输到另一个地方去，  
序列化就是我把房子拆成一个个的砖块放到车子里，  
然后留下一张房子原来结构的图纸，  
反序列化就是我们把房子运输到了目的地以后，  
根据图纸把一块块砖头还原成房子原来面目的过程。  

### **2. 什么时候会用到序列化？**

需要进行`跨平台存储`和`网络传输`的数据，都需要进行序列化。  

```
对象的序列化主要有两种用途：  
1） 把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中；  
2） 在网络上传送对象的字节序列。  
```

### **3. 序列化版本**

在序列化过程中，可以使用序列化对象中的serialVersionUID字段做版本控制。  
一个对象数据，在反序列化过程中，序列化串中的serialVersionUID与当前对象值不同，反序列化会失败。

### **4. java实现序列化**

java 实现序列化有两种方式。  

    1.实现Serializable 接口。  
    2.实现Externalizable 接口(它是Serializable接口的子类)  

**下面是用Serializable接口实现的简单例子:**

```java
import java.io.Serializable;

public class Person implements Serializable{
    // 如果不指定，JDK工具会自动生成对应的版本号，
    // 序列化和反序列化的版本号不一样，则反序列化会失败。
    private final static long serialVersionUID = 123456789L;
    // 年龄
    private int age;
    // 名字
    private String name ;
    // 体重，Transient 属性不会被序列化
    private transient int weight;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getWeight() {
        return weight;
    }

    public void setWeight(int weight) {
        this.weight = weight;
    }
```

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class main {
    public static void main(String[] args) throws Exception {
        FileOutputStream fos = new FileOutputStream("/Users/xxxx/java/temp.txt");
        ObjectOutputStream oos = new ObjectOutputStream(fos);

        Person personOutput = new Person();
        personOutput.setAge(12);
        personOutput.setName("heiheiheiheihei");

        oos.writeObject(personOutput);

        oos.flush();
        oos.close();

        FileInputStream fis = new FileInputStream("/Users/xxxx/java/temp.txt");
        ObjectInputStream oin = new ObjectInputStream(fis);
        Person personInput = (Person) oin.readObject();
        System.out.println("age = " + personInput.getAge());
        System.out.println("name = " + personInput.getName());
        System.out.println("weight = " + personInput.getWeight());
    }
}
```

**执行结果:**

    $ javac main.java && java main
    age = 12
    name = heiheiheiheihei
    weight = 0  //Transient 属性不会被序列化


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`