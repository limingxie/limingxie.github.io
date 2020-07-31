---
title: "【java笔记】反射的简单应用"
date: 2020-07-30T23:28:49+08:00
draft: true
---

今天在代码中看到了invoke方法，了解到原来是使用了反射的机制。   
以前没怎么用过这种方式，所以简单的了解一下。  

具体内容是简单的写了一套代码。

**声明Animal类**
```java
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

class Animal {
    public Animal() {
    }

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    private int age;
    public String name;

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
```

**普通的方法和使用反射**
```java
    @SuppressWarnings("unchecked")
    public void fanshe() throws Exception {
        try {
            System.out.println("-----------正常调用----------");
            Animal animal = new Animal();
            animal.setAge(10);
            System.out.println("animal age:" + animal.getAge());

            System.out.println("-----------使用反射----------");
            // 获取需调用的类
            Class clz = Class.forName("Animal");
            // 获取需调用的函数
            Method setPriceMethod = clz.getMethod("setAge", int.class);
            // 根据class的实例，获取Constructor实例
            Constructor animalConstructor = clz.getConstructor();
            // 根据Constructor实例的newInstance()，获取反射类
            Object animalObj = animalConstructor.newInstance();
            // 使用invoke方法调用反射类的函数
            setPriceMethod.invoke(animalObj, 15);
            // 只调用方法，只需要下面两个步骤
            Method getPriceMethod = clz.getMethod("getAge");
            Object age = getPriceMethod.invoke(animalObj);
            System.out.println("animal age:" + age);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
```

**反射的常用方法**
```java
    @SuppressWarnings("unchecked")
    public void fansheTest() throws Exception {
        try {
            // 使用.forName 获取类的对象
            Class clz1 = Class.forName("java.lang.String");
            System.out.println("-----------Class1----------");
            System.out.println(clz1);


            // 使用 .class 获取类的对象
            Class clz2 = String.class;
            System.out.println("-----------Class2----------");
            System.out.println(clz2);

            // 实例的 .getClass 获取类对象
            String str = new String("Hello");
            Class clz3 = str.getClass();
            System.out.println("-----------Class3----------");
            System.out.println(clz3);


            // 获取反射类，直接调用class的 newInstance()方法获取
            Class clz4 = Animal.class;
            Animal animal4 = (Animal) clz4.newInstance();
            System.out.println("-----------Class4----------");
            System.out.println(animal4.getClass());

            // 获取反射类，通过Constructor的newInstance()方法获取
            Class clz5 = Animal.class;
            Constructor constructor5 = clz5.getConstructor();
            Animal animal5 = (Animal) constructor5.newInstance();
            System.out.println("-----------Class5----------");
            System.out.println(animal5.getClass());

            // 获取反射类，指定构造函数的时候
            Class clz6 = Animal.class;
            Constructor constructor6 = clz4.getConstructor(String.class, int.class);
            Animal animal6 = (Animal) constructor6.newInstance("马", 15);
            System.out.println("-----------Class6----------");
            System.out.println(animal6.getClass());


            Class clz = Animal.class;
            // 获取类的属性(查不到private属性)
            Field[] fields1 = clz.getFields();
            System.out.println("-----------fields1----------");
            for (Field field : fields1) {
                System.out.println(field.getName());
            }

            // 获取声明的类所有属性
            System.out.println("-----------fields2----------");
            Field[] fields2 = clz.getDeclaredFields();
            for (Field field : fields2) {
                System.out.println(field.getName());
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```
**main里执行**
```java
public class main {
    public static void main(String[] args){
        Animal animal = new Animal();
        try{
            animal.fanshe();
            animal.fansheTest();
        }catch (Exception e){
            System.out.println(e.getMessage());
        }
    }
}

```

**编译以后，可以查看结果**
```
$ javac main.java

$ java main
-----------正常调用----------
animal age:10
-----------反射调用----------
animal age:15
-----------Class1----------
class java.lang.String
-----------Class2----------
class java.lang.String
-----------Class3----------
class java.lang.String
-----------Class4----------
class Animal
-----------Class5----------
class Animal
-----------Class6----------
class Animal
-----------fields1----------
name
-----------fields2----------
age
name
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`