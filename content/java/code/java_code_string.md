---
author: "li_mingxie"
title: "【java代码】java 几个有趣的代码问题"
date: 2920-12-20T08:28:49+08:00
tags: [
    "java",
    "String",
    "string",
]
categories: [
    "Java",
]
---

天天都被项目日程勒脖子...^^||
每个bug都是要今晚解决的...^^||

当我们被工作压的喘不了气的时候，不妨休息十分钟。  
看看已下问题缓一缓压力, 估计大部分的现象大神们都应该知道...^^

### 1.下面的代码运行会报错吗？
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

### 2.猜一下输出结果是什么？
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


### 3. 编译后.class文件中的变化
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