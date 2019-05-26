---
layout: post
title: "GO语言中的相关接口"
date: 2019-05-26
description: "GO语言中的相关接口"
tag: GO 从入门到实战
---

* 接口的作用
    * 接口是定义对象之间交互的协议

```go
// 定义一个接口
type Programmer interfase {
    WriteHelloWorld() string
}
// 定义结构体
type GoProgrammer struct {}
// 接口实现[只要是实现接口中所有的方法(方法的签名一致，包括方法名，参数，返回值)，就实现了该接口]
// 不用显示声明 implements
func (p *GoProgrammer) WriteHelloWorld() string {
    fmt.Println("hello world!")
}
```

* 代码示例

```go
package encap_test

import (
	"testing"
)

type Programmer interface {
	WriteHelloWorld() string
}

type GoProgrammer struct {}
// 使用接口中 方法，方法签名一致，就实现了该接口
func (g *GoProgrammer) WriteHelloWorld()string  {
	return "fmt.Println(\"hello world\")"
}

func TestClient(t *testing.T)  {
	var p Programmer
	p = new(GoProgrammer)
	t.Log(p.WriteHelloWorld()) // fmt.Println("hello world")
}
```

* 接口是非入侵性，实现不依赖于接口定义
* 所有接口的定义可以包含在接口使用者包内

* 自定义类型
    * `type IntConvertionFn func(n int) int`
    * `type MyPoint int`

* 程序耗时函数改写

```go
package encap_test

import (
	"fmt"
	"testing"
	"time"
)

type IntConv func(op int) int

func timeSpent(inner IntConv) IntConv  {
	return func(op int) int {
		start := time.Now()
		ret := inner(op)
		fmt.Println("time spent :", time.Since(start).Seconds())
		return ret
	}
}
func slowFun(op int)int  {
	time.Sleep(time.Second * 1)
	return op
}

func TestFun(t *testing.T)  {
	sfFun := timeSpent(slowFun)
	t.Log(sfFun(10))
}

```    