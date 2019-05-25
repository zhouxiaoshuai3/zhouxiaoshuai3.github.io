---
layout: post
title: "GO语言中数组和切片"
date: 2019-05-25
description: "GO语言中数组和切片"
tag: GO 从入门到实战
--- 

* 数组的声明

```go
package array_test

import "testing"

func TestArrayInit(t *testing.T)  {
	// 声明数组，初始化零值
	var arr [3]int
	// 声明数组并且初始化
	arr1 := [3]int {1, 2, 3}
	// 声明多元素的数组，数组的长度是初始值个数的长度
	arr2 := [...]int {1, 2, 3, 4, 5}
	// 可以根据下标可以对数组中元素进行访问和修改
	t.Log(arr[0], arr[1])
	t.Log(arr1, arr2)
}
/**
    array_test.go:12: 0 0
    array_test.go:13: [1 2 3] [1 2 3 4 5]
*/
```

* 数组的遍历
    * 使用 `for` 进行遍历

```go
func TestArrayTravel(t *testing.T)  {
	// 普通使用 for 遍历数据
	arr3 := [...]int{1, 2, 3, 4, 5}
	len := len(arr3)
	for i := 0;i < len ;i++  {
		t.Log(arr3[i])
		/**
			array_test.go:21: 1
		    array_test.go:21: 2
		    array_test.go:21: 3
		    array_test.go:21: 4
		    array_test.go:21: 5
		 */
	}
	// 类似 foreach 的用法，使用 range 关键字
	// 其中 ixd 为数组的下标，可以使用 _ 占位符进行省略
	for ixd, val := range arr3 {
		t.Log(ixd, val)
		/**
			array_test.go:24: 0 1
		    array_test.go:24: 1 2
		    array_test.go:24: 2 3
		    array_test.go:24: 3 4
		    array_test.go:24: 4 5
		 */
	}
}
```    

* 多维数组的声明和遍历

```go
func TestMultiArrayTravel(t *testing.T)  {
	// 多维数组的声明
	// 数组中有 3 个 数组元素，每个数组元素中有 2 个元素
	// 第一个[]是 包含的数组个数，第二个是 每个数组中元素的个数
	arr4 := [3][2]int{ {1, 2}, {3, 4}, {5, 6} }
	for _, v := range arr4 {
		//t.Log(i, v)
		/**
		 	array_test.go:47: 0 [1 2]
		    array_test.go:47: 1 [3 4]
		    array_test.go:47: 2 [5 6]
		 */
		for vi, vv := range v {
			t.Log(vi, vv)
			/**
				array_test.go:57: 0 1
			    array_test.go:57: 1 2
			    array_test.go:57: 0 3
			    array_test.go:57: 1 4
			    array_test.go:57: 0 5
			    array_test.go:57: 1 6
			 */
		}
	}
}
```

* 数组的截取:返回截取后的数组。
    * arr[开始索引（包含），结束索引（不包含）].包头不保尾
    * 不支持负数索引

```go
func TestSubArray(t *testing.T)  {
	arr5 := [...]int{1, 2, 3, 4, 5, 6, 7}
	t.Log(arr5[1:2]) // [2]
	t.Log(arr5[1:3]) // [2 3]
	t.Log(arr5[:]) // [1 2 3 4 5 6 7]
	t.Log(arr5[:5]) // [1 2 3 4 5]
	t.Log(arr5[2:]) // [3 4 5 6 7]
}
```
* 切片(slice)的内部结构
    
    ![](https://upload-images.jianshu.io/upload_images/7303277-a1a6c3469fc018cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    
        * 切片是一个结构体，结构体内由三部分组成 `ptr`, `len`, `cap`
        * `ptr` ：指针，指向内部的一个数组；
        * `len` : 可以访问的元素个数
        * `cap` : 指针指向内部数组的容量


* 切片共享存储结构
    ![](https://upload-images.jianshu.io/upload_images/7303277-0f92ba4dd0e4934f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)    
    
    * slice 可变长度
    
    ```go
    func TestSliceGrowing(t *testing.T)  {
	// slice 是如何可变长的
	s := []int{}
	for i := 0; i < 10 ;i++  {
		s = append(s, i)
		t.Log(len(s), cap(s))
		// 当 slice 中的 len 超过原有的 cap 时，cap 的增长是前一次的 2 倍
		// 当 slice 在不断增长是，在内部会创建一个新的存储空间来保存 新的slice，
		// 并把原有的存储空间拷贝到新的存储空间中 所以需要赋值给新的slice
		/**
		 	slice_test.go:27: 1 1
		    slice_test.go:27: 2 2
		    slice_test.go:27: 3 4
		    slice_test.go:27: 4 4
		    slice_test.go:27: 5 8
		    slice_test.go:27: 6 8
		    slice_test.go:27: 7 8
		    slice_test.go:27: 8 8
		    slice_test.go:27: 9 16
		    slice_test.go:27: 10 16
		 */
	}
}
    ```
    
    * slice 共享空间

    ```go
    func TestSliceShareMemory(t *testing.T)  {
	year := []string{"Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug",
		"Sep", "Oct", "Nov", "Dec"}
	Q2 := year[3:6]

	t.Log(Q2, len(Q2), cap(Q2))
	// [Apr May Jun] 3 9  len(Q2) 是 3 容易理解，截取下标是 3 的元素到下标是 6 的元素[不包含]
	// cap(Q2) 为什么是 9 呢？ 是因为 从 数组下标 3 开始到数组的最后一个元素，指向了内部的连续存储空间 一个 9 个元素；
	summer := year[5:8]
	t.Log(summer, len(summer), cap(summer))
	// [Jun Jul Aug] 3 7 同理
	summer[0] = "Unkown"
	t.Log(Q2) // [Apr May Unkown] 两个切片 共同的部分被影响；因为修改了同一个存储空间
	t.Log(year) // [Jan Feb Mar Apr May Unkown Jul Aug Sep Oct Nov Dec]
}
    ```
    
* 数组和切片的对比
    * 容量是否可伸缩
        * 数组是容量不可伸缩的，切片可以伸缩
    * 是否可以进行比较
        * 相同维度，相同元素，数组中每一个元素都相同时可以比较的
        * 切片不可以做比较

        ```go
        func TestSliceComparing(t *testing.T)  {
        	s := []int{}
        	s1 := []int{}
        	t.Log(s == s1) // invalid operation: s == s1 (slice can only be compared to nil)
        }
        ```