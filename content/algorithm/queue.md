---
author: "li_mingxie"
title: "【算法笔记】queue的简单实践"
date: 2022-01-29T07:28:49+08:00
tags: [
    "Linked List",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

今天用go语言简单的写了一下queue的方法。  
为了以后方便查看，当做笔记整理了一下~~  


## 1.链表(Linked List)

[维基百科](https://zh.wikipedia.org/wiki/%E9%93%BE%E8%A1%A8)里是这么解释的。

> 链表（Linked list）是一种常见的基础数据结构，是一种线性表，  
> 但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的指针(Pointer)。  
> 由于不必须按顺序存储，链表在插入的时候可以达到O(1)的复杂度，比另一种线性表顺序表快得多，  
> 但是查找一个节点或者访问特定编号的节点则需要O(n)的时间，而顺序表相应的时间复杂度分别是O(logn)和O(1)。 

看看下图:  

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_1.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_1.png)

简单的说就是**把数据链接起来保存的数据结构。**  
因为需要链接，所以每个节点都需要存储下一个节点的信息。  
这一特性是链表的插入和删除比较快。查询慢(因为只记录下一个节点，想找特定的值只能全部遍历)。  
但是数组就恰恰相反，查询很快，插入和删除的效率就不高了。  
可以看看下图：  

#### 链表的插入

只要把3和7的中间加入5就行了。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_11.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_11.png)

#### 链表的删除

这就更简单了，直接把3连接到7就行了。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_1.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_12.png)

#### 数组的插入

先把7和9各向右挪一格，然后把5放进去。如果是个很长的数组，这效率就可想而知了。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_21.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_21.png)

#### 数组的删除

这也是先吧5删除后，再把7和9挪到左边。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_31.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_31.png)


##### 这里简单的说明一下，二叉树就是有链表的插入和删除的高效率，而且又有不慢的查询速度的数组和链表相结合的数据结构。

## 2.链表的结构

链表有好几种结构，但是比较常有的有3种。  
单向链表，双向链表，循环链表。

#### 单向链表

链表中最简单的一种是单向链表，它包含两个域，一个信息域和一个指针域。这个链接指向列表中的下一个节点，而最后一个节点则指向一个空值。
看下图应该很容易理解。  

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_2.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_2.png)

#### 双向链表

每个节点有两个连接：一个指向前一个节点，而另一个指向下一个节点。  

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_3.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_3.png)


#### 循环链表

首节点和末节点被连接在一起。形成一个循环。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_4.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_4.png)


## 3.单向链表的实例

#### struct

```go
//链表
type LinkedList struct {
	Head *ListNode  //头部节点
	Tail *ListNode  //尾部节点
	Size int        //链表长度
}

//节点
type ListNode struct {
	Value int   //节点的值
	Next  *ListNode //下一个节点
}
```

#### Insert

```go
//position: 要插入的位置
//number: 要插入的值
func (node *LinkedList) Insert(position int, number int) {
	if position > node.Size {
		return
	}

	newNode := &ListNode{Value: number}

	if position == 0 {
		newNode.Next = node.Head
		node.Head = newNode
		if node.Tail == nil {
			node.Tail = newNode
		}
	} else if position == node.Size {
		node.Tail.Next = newNode
		node.Tail = newNode
	} else {
		currentNode := node.Head
		for i := 0; i < position; i++ {
			currentNode = currentNode.Next
		}

		newNode.Next = currentNode.Next
		currentNode.Next = newNode
	}
	node.Size++
}
```

#### Delete

```go
//number: 需要删除的值
func (node *LinkedList) Delete(number int) {
	if node.Head == nil || node.Tail == nil {
		return
	}

	if node.Head.Value == number {
		node.Head = node.Head.Next
		node.Size--
		if node.Size == 0 {
			node.Tail = node.Head
		}
		return
	}

	preNode := node.Head
	curNode := node.Head
	for i := 0; i < node.Size; i++ {
		if curNode.Value == number {
			if curNode == node.Tail {
				node.Tail = preNode
			}
			preNode.Next = curNode.Next
			node.Size--
			return
		}
		preNode = curNode
		curNode = curNode.Next
	}
}
```

#### Update

```go
func (node *LinkedList) Update(old_number int, new_number int) int {
	currentNode := node.Head
	for i := 0; i < node.Size; i++ {
		if currentNode.Value == old_number {
			currentNode.Value = new_number
			return i
		}
		currentNode = currentNode.Next
	}
	return -1
}
```

#### Search

```go
func (node *LinkedList) Search(number int) int {
	currentNode := node.Head
	for i := 0; i < node.Size; i++ {
		if currentNode.Value == number {
			return i
		}
		currentNode = currentNode.Next
	}
	return -1
}
```

#### Print

```go
func (node *ListNode) Print() {
	if node == nil || node.Value == 0 {
		return
	} else {
		fmt.Print(node.Value, " ")
		node.Next.Print()
	}
}
```

#### 执行结果

```go
func main() {
	var linkedlist LinkedList
	linkedlist.Insert(0, 1)
	linkedlist.Insert(1, 2)
	linkedlist.Insert(2, 3)
	linkedlist.Insert(3, 4)
	linkedlist.Insert(4, 5)
	linkedlist.Insert(5, 6)
	linkedlist.Insert(6, 7)
	linkedlist.Insert(7, 8)
	fmt.Println("----insert 1,2,3,4,5,6,7,8----")
	linkedlist.Head.Print()
	fmt.Println("")
	fmt.Println("------update 5 => 55 ------")
	linkedlist.Update(5, 55)
	linkedlist.Head.Print()
	fmt.Println("")
	fmt.Println("-------- delete 55 --------")
	linkedlist.Delete(55)
	linkedlist.Head.Print()
	fmt.Println("")
	fmt.Println("-------- delete 9 --------")
	linkedlist.Delete(9)
	linkedlist.Head.Print()
	fmt.Println("")
	fmt.Println("-------- search 4  --------")
	fmt.Println("index:", linkedlist.Search(4))
}
```

执行结果为：
```
$ go run main.go
----insert 1,2,3,4,5,6,7,8----
1 2 3 4 5 6 7 8 
------update 5 => 55 ------
1 2 3 4 55 6 7 8
-------- delete 55 --------
1 2 3 4 6 7 8
-------- delete 9 --------
1 2 3 4 6 7 8
-------- search 4  --------
index: 3
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`