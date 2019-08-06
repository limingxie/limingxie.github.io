---
title: "递归(recursive)"
date: 2019-08-03T07:28:49+08:00
draft: true
---

最近在研究算法...^^ 算法中最基本的是排序。  
排序当中最常见的是快速排序(Quick Sort)。  
理解快速排序的代码，那就离不开递归了。  
递归以前听过，但是真正去了解和研究是最近为了对算法的了解。

> 递归感觉很绕？  

但是理解其原理的话还是可以接受的，甚至有时候会喜欢这种方式。  

> 递归不像传说中的那么麻烦。弄清楚   
> 基线条件（base case）和 递归条件（recursive case）

绝大多数的递归都是这种结构

```
function(parameter){
    if 基线条件（base case）{ //离开递归的条件，这条件如果不明确，或是满足不了。很容易进入死循环。
        return
    }

    XXXX...XXXX //处理业务逻辑

    if 递归条件（recursive case）{ //进入递归的条件
        function(parameter)
    }
}
```
---------------------
来看看一个简单的例子吧。详细内容可以看这里  
https://pintia.cn/problem-sets/994805260223102976/problems/994805325918486528

```
卡拉兹(Callatz)猜想：

对任何一个正整数 n，如果它是偶数，那么把它砍掉一半；
如果它是奇数，那么把 (3n+1) 砍掉一半。这样一直反复砍下去，最后一定在某一步得到 n=1。
我们假设 n < 1000

输入样例：
3
输出样例：
5
```

一般情况下我们是直接用while或for循环处理。

```C
#include <stdio.h>

int main()
{
    int step = 0; //记录步数
    int n = 0; 

    scanf("%d", &n); 
    while (n != 1)
    {
        if (n % 2 == 0) //判断是不是偶数
        {
            n = n / 2;
        }
        else
        {
            n = (3 * n + 1) / 2;
        }
        step++; //+步数
    }
    printf("%d\n", step);
    return 0;
}
```
---------------------

那用递归如何去写呢？

我们先看看这里的 `基线条件(base case)` 和 `递归条件(recursive case)`是什么?

```
function(parameter){
    if n == 1 //基线条件(base case)
        return
    }

    XXXXX // 这里需要些算法逻辑

    function(parameter){} //递归条件(recursive case)什么时候进入递归呢？ 是没满足基线条件
}
```

只要找到`基线条件(base case)` 和 `递归条件(recursive case)`这代码就好写了。  
我们看看详细的代码？

```C
#include <stdio.h>

void b1001(int &step, int n) // 这里是step的引用值，不想这么用的话直接上面声明全局变量
{
    if (n == 1 or n == 0) //基线条件（base case）满足这条件就退出
    {
        return;
    }

    if (n % 2 == 0)     //这个if else 是处理算法逻辑
    {
        n = n / 2;
    }
    else
    {
        n = (3 * n + 1) / 2;
    }

    step = step + 1; // 处理算法逻辑后 加步骤数

    b1001(step, n); //递归条件(recursive case)是没满足 基线条件(base case)而进入递归方法
}

int main()
{
    int step, n = 0; //步数
    int n = 0; //接收值

    scanf("%d", &n);
    b1001(step, n);
    printf("%d\n", step);
    return 0;
}

```

大部分的递归逻辑都不会离开这最基本的模式。  
不要一看到递归就害怕。静下心来先找找  
`基线条件(base case)` 和 `递归条件(recursive case)`  
会很容易的看出里面的逻辑的。 

超级简单吧？
(更复杂的方式会在后面的快速排序，深度搜索等算法里去介绍了...)

还有平时处理公司业务逻辑的时候, `能用循环解决的尽量不要用递归。`    
**大部分场景是可以用循环解决问题的。别把事情搞大..**  
原因很简单，毕竟大部分的队友会看到递归的代码第一反应是: NND 这是谁写的方法？

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`