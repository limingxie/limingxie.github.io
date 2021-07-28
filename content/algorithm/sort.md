---
author: "li_mingxie"
title: "算法(排序)"
date: 2019-07-09T07:28:49+08:00
tags: [
    "排序",
    "sort",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

感觉现在越来越重视算法了。<!--more-->  
因为我不是计算机专业的，所以对这些算法没怎么注意过。  
这么高年龄的程序员居然才接触到这些算法，真是惭愧啊...^^;;

以下代码都是用golang写的，算是给自己看的笔记~

```
(1) 冒泡排序(Bubble Sort)
(2) 选择排序(Selection Sort)
(3) 插入排序(Insertion Sort)
(4) 希尔排序(Shell Sort)
(5) 归并排序(Merge Sort)
(6) 快速排序(Quick Sort)
(7) 基数排序(Radix Sort)
(8) 堆排序(Heap Sort)
```

<br/>

**◆ 冒泡排序(Bubble Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/bubble_sort_03.gif)  
![bubble_sort_03](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/bubble_sort_03.gif)

```go
//泡沫排序
func BubbleSort() {
	data := []int{8, 100, 99, 50, 22, 15, 16, 2, 99, 1000, 999, 1}

	for i := 0; i < len(data); i++ {
		for j := 0; j < len(data)-1-i; j++ {
			if data[j] > data[j+1] {
				v := data[j]
				data[j] = data[j+1]
				data[j+1] = v
			}
		}
	}

	fmt.Println(data)
}
```

--------------------------------------------
**◆ 选择排序(Selection Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/selection_sort_01.gif)  
![selection_sort_01](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/selection_sort_01.gif)  

```go
//选择排序
func SelectionSort() {
	data := []int{8, 100, 99, 50, 22, 15, 16, 2, 99, 1000, 999, 1}

	var book int
	for i := 0; i < len(data); i++ {
		book = i
		for j := i; j < len(data); j++ {
			if data[book] > data[j] {
				book = j
			}
		}
		v := data[i]
		data[i] = data[book]
		data[book] = v
	}

	fmt.Println(data)
}
```

--------------------------------------------
**◆ 插入排序(Insertion Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/insertion_sort_01.gif)  
![insertion_sort_01](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/insertion_sort_01.gif)  

```go
//直接插入排序。因为我太不喜欢直接串改数组数据，所以写的有点像倒过来的冒泡排序
func InsertionSort() {
	data := []int{8, 100, 99, 50, 22, 15, 16, 2, 99, 1000, 999, 1}

	var v int
	for i := 0; i < len(data); i++ {
		for j := i; j > 0; j-- {
			if data[j] < data[j-1] {
				v = data[j]
				data[j] = data[j-1]
				data[j-1] = v
			} else {
				break
			}
		}
	}

	fmt.Println(data)
}
```

**◆ 希尔排序(Shell Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/shell_sort_01.gif)  
![shell_sort_01](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/shell_sort_01.gif)  

```go
//网上的优雅的写法
func ShellSort() {
	data := []int{8, 100, 99, 50, 22, 15, 16, 2, 99, 1000, 999, 1}

	for step := len(data) / 2; step > 0; step /= 2 {
		//开始插入排序
		for i := step; i < len(data); i++ {
			//满足条件则插入
			for j := i - step; j >= 0 && data[j+step] < data[j]; j -= step {
				data[j], data[j+step] = data[j+step], data[j]
			}
		}
	}

	fmt.Println(data)
}

//我自己琢磨了半天才写出来的复杂版..ㅠㅠ
func ShellSort1() {
	data := []int{8, 100, 99, 50, 22, 15, 16, 2, 99, 1000, 999, 1}

	//先按照增量循环
	for step := len(data) / 2; step > 0; step = step / 2 {
		//每次增量的循环数
		for i := 0; i < step; i++ {
			//插入排序
			for j := i; j < len(data); j = j + step {
				for k := i; k < j; k = k + step {
					if data[k] > data[j] {
						v := data[j]
						//为了插入往右挪数据
						for l := j; l > k; l = l - step {
							data[l] = data[l-step]
						}
						data[k] = v
					}
				}

			}
		}
	}

	fmt.Println(data)
}
```

--------------------------------------------
**◆ 归并排序(Merge Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/merge_sort_01.gif)  
![merge_sort_01](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/merge_sort_01.gif)  
#### **未完待续**

--------------------------------------------
**◆ 快速排序(Quick Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/quick_sort_02.gif)  
![quick_sort_02](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/quick_sort_02.gif)  
#### **未完待续**
--------------------------------------------
**◆ 基数排序(Radix Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/radix_sort_01.gif)  
![radix_sort_01](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/radix_sort_01.gif)  

--------------------------------------------
**◆ 堆排序(Heap Sort)**  
[图片备用地址](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/heap_sort_01.gif)  
![heap_sort_01](https://pangpangimg.oss-cn-beijing.aliyuncs.com/gif/sort/heap_sort_01.gif) 
#### **未完待续**

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`