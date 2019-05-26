---
layout: post
title: "GO语言中行为的定义和实现"
date: 2019-05-26
description: "GO语言中行为的定义和实现"
tag: GO 从入门到实战
---

* 封装数据和行为

```go
type Employee struct {
    Id string
    Name string
    Age int
}
```

* 实例创建及初始化

```go
e := Employee{"0", "Bob", 20}
e1 := Employee{Name:"Mike", Age:30}
e2 := new(Employee) //[使用 new 创建一个指向实例的指针] 注意这里返回的引用/指针，相当于 e:=&Employee{}
e2.Id = "2" // 通过实例的指针访问成员不需要使用 ->
e2.Age = 29
e2.Name = "Rose"
```

* 行为(方法)定义

```go
type Employee struct {
    Id string
    Name string
    Age int
}
// 第一种定义方法
// 在实例对应方法被调用时，实例的成员进行值复制
func (e Employee) String() string {
    return fmt.Sprintf("id:%s-Name:%s-Age:%d", e.Id, e.Name, e.Age)
}
// 第二种方法
// 通常情况下为了避免内存拷贝我们使用这种方式
func (e *Employee) String() string{
    return fmt.Sprintf("id:%s-Name:%s-Age:%d", e.Id, e.Name, e.Age)
}
```

* Go 函数中，不管是通过指针还是实例访问，表述都是一样的；

```go
package encap_test

import (
	"fmt"
	"testing"
)

type Employee struct {
	Id string
	Name string
	Age int
}

func (e Employee) String()string  {
	return fmt.Sprintf("id:%s-name:%s-age:%d", e.Id, e.Name, e.Age)
}
func (e *Employee) Strings()string  {
	return fmt.Sprintf("id:%s-name:%s-age:%d", e.Id, e.Name, e.Age)
}

func TestStructOp(t *testing.T)  {
	e := Employee{"0", "Bob", 20}
	t.Log(e.String()) // id:0-name:Bob-age:20
	t.Log(e.Strings()) // 实例得到的是一个类型的指针 id:0-name:Bob-age:20
}
```