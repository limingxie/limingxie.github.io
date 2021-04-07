---
author: "li_mingxie"
title: "【java代码】java 几个有趣的代码问题"
date: 2920-12-20T08:28:49+08:00
tags: [
    "java",
    "String",
]
categories: [
    "Java",
]
---

天天都被项目日程勒脖子...^^||
每个bug都是要今晚解决的...^^||

当我们被工作压的喘不了气的时候，不妨休息十分钟。  
看看已下问题缓一缓压力, 估计大部分的现象大神们都应该知道...^^

### 1.猜一下输出结果是什么？
```java
@Test
public void testInteger(){
    Integer a = 127;
    Integer b = 127;
    Integer c = 128;
    Integer d = 128;

    System.out.println(a == b);
    System.out.println(a.equals(b));
    System.out.println(c == d);
    System.out.println(c.equals(d));
}
```
执行结果

### 2.下面的代码运行会报错吗？
```java
@Test
//运行时会报错吗？
public void testString() {
    char data[] = {'a', 'b', 'c'};
    String aa = new String(data, 3, 0);
    System.out.println(aa);
}
```
不会报错的，原因可以看String类的源代码，坑爹...

### 3.那这个String又怎么解释？
```java
@Test
public void testString1() {
    Integer a1 = 128;
    Integer a2 = 128;

    String b1 = "aaaa";
    String b2 = "aaaa";

    String c1 = new String("aaaa");
    String c2 = new String("aaaa");

    System.out.println("--------Integer--------");

    System.out.println(System.identityHashCode(a1));
    System.out.println(System.identityHashCode(a2));

    System.out.println("--------String--------");

    System.out.println(System.identityHashCode(b1));
    System.out.println(System.identityHashCode(b2));

    System.out.println("--------new String--------");

    System.out.println(System.identityHashCode(c1));
    System.out.println(System.identityHashCode(c2));
}
```
执行结果

```
在JVM中存在一个字符串池，其中存有很多String对象，并且可以被共享使用。当创建一个字符串常量时，例如String s="abc"，会首先在字符串常量池中查找是否存在相同的字符串定义，若已经定义，则直接引用其定义，此时不需要创建新的对象；

1.直接赋值
在采用直接赋值的处理过程之中，可以自动将对象保存到字符串对象池中，这样如果有相同数据定义时，首先会去找字符串对象池中是否有相同的对象，如果有就直接指向他，这样可以实现对象的重用，从而减少对象的产生。
例如：

String a = "aishu"; //直接赋值
String b = "aishu";// b指向的“aishu”与a指向的“aishu”是字符串词中的同一个数据。
a == b; // true
2.构造方法实例化
会产生两个实例化对象其中一个是匿名对象（在堆内存中，并且在栈内存中无引用指向他，所以便产生了垃圾对象）。另一个是new出来的对象，供程序使用。
不会自动保存到对象池中，无法实现对象的重用
可以调用intern()方法手动将其保存到对象池中
使用以下例子具体分析:

String a = new String("aishu");//构造方法实例化
内存分析：
"aishu"在程序中是一个在堆内存中的匿名对象，然后用new开辟了一个新的堆内存存放“aishu”,供程序使用。匿名对象就在堆内存中无引用指向他,所以他就成了内存中的垃圾对象。占用程序内存。
```

### 4. 编译后.class文件中的变化
```java
public class TestFinal {
    final int a = 0;
    int b = 0;

    public static void testFinal1(final int arg) {
        final int var = 1;
        System.out.println(var + arg);
    }

    public static void testFinal2(int arg) {
        int var = 1;
        System.out.println(var + arg);
    }
}
```
用IDEA查看.class 文件
```java
public class TestFinal {
    final int a = 0;
    int b = 0;

    public TestFinal() {
    }

    public static void testFinal1(int var0) {
        System.out.println(1 + var0);
    }

    public static void testFinal2(int var0) {
        byte var1 = 1;
        System.out.println(var1 + var0);
    }
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`