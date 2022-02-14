---
author: "li_mingxie"
title: "【算法笔记】stack的简单实践"
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

今天用go语言简单的写了一下stack的方法。  
为了以后方便查看，当做笔记整理了一下~~  


## 1.栈(Stack)

[维基百科](https://zh.wikipedia.org/wiki/%E5%A0%86%E6%A0%88)里是这么解释的。

> 是计算机科学中的一种抽象资料类型，只允许在有序的线性资料集合的一端（称为堆栈顶端，英语：top）进行加入数据（英语：push）和移除数据（英语：pop）的运算。  
> 因而按照后进先出（LIFO, Last In First Out）的原理运作。  

具体的详解可以参考这篇文章里的Stack部分[栈(STACK), 堆(HEAP), 队列(QUEUE) 是什么？](https://limingxie.github.io/basic/stack/)

## 2.数组栈

#### struct

```go
type Stack struct {
	Capacity int
	Top      int
	Values   []int
}
```

#### 进栈(push)

```go
func (s *Stack) Push(val int) bool {
	if s.Top >= s.Capacity {
		fmt.Println("Stack Overflow!")
		return false
	}
	s.Top++
	s.Values[s.Top] = val
	return true
}
```

#### 出栈(pop)

```go
func (s *Stack) Pop() int {
	if s.Top < 0 {
		fmt.Println("Stack Underflow!")
		return 0
	}
	result := s.Values[s.Top]
	s.Top--
	return result
}
```

####  查看栈顶元素(peek)和IsEmpty()

```go
func (s *Stack) Peek() int {
	if s.Top < 0 {
		fmt.Println("Stack Underflow!")
		return 0
	}
	result := s.Values[s.Top]
	return result
}

func (s *Stack) IsEmpty() bool {
	return s.Top < 0
}
```

#### 执行结果

```go
func main() {
	stack := Stack{}
	stack.Capacity = 10
	stack.Top = -1
	stack.Values = make([]int, stack.Capacity)

	stack.Push(10)
	stack.Push(11)
	stack.Push(12)
	fmt.Println("-------- stack.Values  --------")
	fmt.Printf("%+v", stack.Values)
	fmt.Println("")
	fmt.Println("-------- stack.IsEmpty()  --------")
	fmt.Println(stack.IsEmpty())
	fmt.Println("-------- stack.Pop()  --------")
	fmt.Println(stack.Pop())
	fmt.Println("-------- stack.Peek()  --------")
	fmt.Println(stack.Peek())
```

执行结果为：
```
$ go run main.go
-------- stack.Values  --------
[10 11 12 0 0 0 0 0 0 0]
-------- stack.IsEmpty()  --------
false
-------- stack.Pop()  --------
12
-------- stack.Peek()  --------
11
```

## 3.链式栈

#### struct

```go
type LinkStack struct {
	Top *StackNode
}

type StackNode struct {
	Value int
	Next  *StackNode
}
```

#### 进栈(push)

```go
func (s *LinkStack) Push(val int) bool {
	newNode := StackNode{Value: val}
	if s.Top == nil {
		s.Top = &newNode
	} else {
		newNode.Next = s.Top
		s.Top = &newNode
	}
	return true
}
```

#### 出栈(pop)

```go
func (s *LinkStack) Pop() int {
	if s.Top == nil {
		fmt.Println("stack is empty!")
		return -1
	}
	result := s.Top
	s.Top = result.Next
	return result.Value
}
```

####  查看栈顶元素(peek)和IsEmpty()，Print()

```go
func (s *LinkStack) Peek() int {
	if s.Top == nil {
		fmt.Println("stack is empty!")
		return -1
	}
	return s.Top.Value
}

func (s *LinkStack) IsEmpty() bool {
	return s.Top == nil
}

func (node *StackNode) Print() {
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
	linkStack := LinkStack{}
	linkStack.Top = &StackNode{Value: 10}
	linkStack.Top.Next = &StackNode{Value: 11}
	linkStack.Top.Next.Next = &StackNode{Value: 12}

	fmt.Println("-------- linkStack.Values  --------")
	linkStack.Top.Print()
	fmt.Println("")
	fmt.Println("-------- stack.IsEmpty()  --------")
	fmt.Println(linkStack.IsEmpty())
	fmt.Println("-------- stack.Pop()  --------")
	fmt.Println(linkStack.Pop())
	fmt.Println("-------- stack.Peek()  --------")
	fmt.Println(linkStack.Peek())
	fmt.Println("-------- linkStack.Values  --------")
	linkStack.Top.Print()
```

执行结果为：
```
$ go run main.go
-------- linkStack.Values  --------
10 11 12 
-------- stack.IsEmpty()  --------
false
-------- stack.Pop()  --------
10
-------- stack.Peek()  --------
11
-------- linkStack.Values  --------
11 12 
```



----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`