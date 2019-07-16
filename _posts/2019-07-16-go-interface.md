---
layout: post
title: "GO 接口 duck typing的概念"
date: 2019-07-16
description: "GO 接口 duck typing的概念"
tag: GO 资深工程师深度讲解
---

* duck typing

> 像鸭子走路，像鸭子叫，那么就是鸭子

* 描述事物的外部行为而非内部结构
* 严格说 GO 语言属于结构化类型系统，和具体结构无关。类似 duck typing 
* go 语言的接口是由**使用者**定义

#### 接口的定义与实现

* 接口的实现时隐式的，只要实现接口中的方法

```go
/**
    main.go
*/
package main

import (
	"fmt"
	"learnGo/retriever/mock"
)

type Retriever interface {
	Get(url string) string
}

func download(r Retriever) string {
	return r.Get("http://www.baidu.com")
}

func main()  {
	var r Retriever
	r = mock.Retriever{"this is a fake baidu.com"}
	fmt.Println(download(r))
}

```
```go
/**
    mock.go
*/
package mock

type Retriever struct {
	Contents string
}

func (r Retriever) Get (url string) string  {
	return r.Contents
}

```
#### 接口的值类型
* 接口变量自带指针
* 接口变量同样采用值传递，几乎不需要使用接口的指针
* 指针接收者实现只能以指针方式使用，值接收者都可