---
title: "【java笔记】类, 抽象类, 接口的简单实验"
date: 2020-07-26T07:28:49+08:00
draft: true
---

要接触，抽象，继承，多态，封装的java了。  
为了加深理解自己做了一个简单的实验。  

> 
1.类只能继承一个。  
2.抽象类也只能继承一个。  
3.接口可以继承多个。  
4.不能同时继承类和接口，也不能同时继承抽象类和接口。  

我先创建了以下文件。
[图片备用地址](https://limingxie.github.io/images/java/abstract/abstract_test1.png)  
![文件目录结构](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/java/abstract/abstract_test1.png)

先看看每个文件的代码


**【AnimalAbstract】抽象类里添加了2个函数，其中一个是抽象函数。**
```java
package animals.animal;

public abstract class AnimalAbstract {
    public void eat(String name){
        System.out.println("eat:" +  AnimalAbstract.class + " | name:" + name);
    };
    public abstract void sleep(String name);
}
```

**【AnimalClass】一个普通的类和函数。**
```java
package animals.animal;

public class AnimalClass {
    public void smile(String name){
        System.out.println("smile:" +  AnimalAbstract.class + " | name:" + name);
    };
}
```

**【AnimalClass】两个不同的接口。**
```java
package animals.animal;

public interface AnimalInterfaceJump {
    public void jump (String name);
}
```

```java
package animals.animal;

public interface AnimalInterfaceRun {
    public void run (String name);
}
```

**【Cat】继承了一般类**
```java
package animals.MammalInt;

import animals.animal.*;

public class Cat extends AnimalClass {
}
```

**【Dog】继承了抽象类，实现了AnimalAbstract的sleep函数**
```java
package animals.MammalInt;

import animals.animal.AnimalAbstract;

public class Dog extends AnimalAbstract {

    @Override
    public void sleep(String name) {
        System.out.println("sleep:" +  Dog.class + " | name:" + name);
    }
}
```

**【Sheep】继承了两个接口，以及实现了两个接口的函数**
```java
package animals.MammalInt;

import animals.animal.AnimalInterfaceRun;
import animals.animal.AnimalInterfaceJump;

public class Sheep implements AnimalInterfaceRun, AnimalInterfaceJump {

    @Override
    public void run(String name) {
        System.out.println("run:" +  Sheep.class + " | name:" + name);
    }

    @Override
    public void jump(String name) {
        System.out.println("jump:" +  Sheep.class + " | name:" + name);
    }
}
```

```java
package animals;

import animals.animal.AnimalAbstract;
import animals.animal.AnimalClass;
import animals.animal.AnimalInterfaceRun;
import animals.animal.AnimalInterfaceJump;
import animals.MammalInt.Cat;
import animals.MammalInt.Dog;
import animals.MammalInt.Sheep;

public class main {
    public static void main(String[] args) {
        System.out.println("-------------class------------");
        // 继承一般类可以自由调用父类的函数
        AnimalClass animalClass = new Cat();
        animalClass.smile("catName");
        System.out.println("-------------abstract------------");
        // 继承抽象类，可以调用实现后的函数
        AnimalAbstract animalAbstract = new Dog();
        animalAbstract.sleep("dogName");
        animalAbstract.eat("dogName");
        System.out.println("-------------interface------------");
        // 只能调用接口内的实现好的函数
        AnimalInterfaceRun animalInterfaceRun = new Sheep();
        animalInterfaceRun.run("sheepName");
        //animalInterfaceRun.jump("sheepName"); 这么写会出错，原因大家应该明白...^^
        AnimalInterfaceJump animalInterfaceJump = new Sheep();
        animalInterfaceJump.jump("sheepName");
    }
}
```

**编译以后，可以查看结果**
```
$ javac animals/animal/AnimalAbstract.java animals/animal/AnimalClass.java animals/animal/AnimalInterfaceRun.java animals/animal/AnimalInterfaceJump.java animals/MammalInt/Cat.java animals/MammalInt/Dog.java animals/MammalInt/Sheep.java animals/main.java

$ java animals.main
-------------class------------
smile:class animals.animal.AnimalAbstract | name:catName
-------------abstract------------
sleep:class animals.MammalInt.Dog | name:dogName
eat:class animals.animal.AnimalAbstract | name:dogName
-------------interface------------
run:class animals.MammalInt.Sheep | name:sheepName
jump:class animals.MammalInt.Sheep | name:sheepName
```

做了最最基本的简单测试，别见笑...^^

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`