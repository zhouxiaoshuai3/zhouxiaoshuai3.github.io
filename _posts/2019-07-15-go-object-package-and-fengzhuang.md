---
layout: post
title: "GO 语言的面向`包和封装`"
date: 2019-07-15
description: "GO 语言的面向`包和封装`"
tag: GO 资深工程师深度讲解
---

#### 封装
* 名字一般使用 `大驼峰` 命名方法
* 首字母大写：`public`
* 首字母小写：`private` 
* 以上封装不仅限于方法，也用于常量，结构等；
* 这些封装权限是对于包来区别的

#### 包
* 每个目录是一个包，每个目录只能有一个包
* `main` 包包含可执行入口【main函数】
* 为结构定义的方法必须放在同一个包内
* 可以是不同的文件

* 拆分原有 nodeTree 代码：

```go
/**
    entry.go
*/ 
package main

import "learnGo/object/tree"

func main() {
	var root tree.TreeNode
	root = tree.TreeNode{Value:100}
	root.Left = &tree.TreeNode{}
	root.Right = &tree.TreeNode{5, nil, nil}
	root.Right.Left = new(tree.TreeNode)
	root.Left.Right = tree.CreateNode(2)
	root.Right.Left.SetValue2(4)
	root.Traverse()
}

```

```go
/**
    node.go
*/
package tree

import (
	"fmt"
)

/**
声明结构体
*/
type TreeNode struct {
	Value int
	Left, Right *TreeNode
}

/**
工厂函数
*/
func CreateNode(Value int) *TreeNode  {
	return &TreeNode{Value:Value}
}

/**
TreeNode 方法
*/
func (node TreeNode) Print()  {
	fmt.Println(node.Value)
}
/**
修改node 的值,值传递
*/
func (node TreeNode) SetValue(Value int)  {
	node.Value = Value
}
/**
修改 node 的值，引用传递
*/
func (node *TreeNode) SetValue2(Value int)  {
	if node == nil {
		fmt.Println("node is nil")
		return
	}
	node.Value = Value
}
```

```go
/**
    Traverse.go
*/
package tree

/**
树的遍历，中树遍历
*/
func (node *TreeNode) Traverse()  {
	if node == nil {
		return
	}
	node.Left.Traverse()
	node.Print()
	node.Right.Traverse()

}

```

* 在入口函数 `main` 中引用 路径，就可以把方法拆分到不同的文件中，不同文件可以调用使用


* 如何扩充系统类型或者别人的类型？
* 定义别名

    ```go
    package main

    import (
    	"fmt"
    	"learnGo/google工程师讲解go/object/tree"
    )
    
    type myTreeNode struct {
    	node *tree.Node
    }
    
    func (myNode *myTreeNode) postOrder() {
    	if myNode == nil || myNode.node == nil {
    		return
    	}
    	left := myTreeNode{myNode.node.Left}
    	left.postOrder()
    	right := myTreeNode{myNode.node.Right}
    	right.postOrder()
    	myNode.node.Print()
    }
    
    func main() {
    	var root tree.Node
    	root = tree.Node{Value: 100}
    	root.Left = &tree.Node{}
    	root.Right = &tree.Node{5, nil, nil}
    	root.Right.Left = new(tree.Node)
    	root.Left.Right = tree.CreateNode(2)
    	root.Right.Left.SetValue2(4)
    	root.Traverse()
    
    	fmt.Println()
    	node := myTreeNode{&root}
    	node.postOrder()
    	fmt.Println()
    
    }

    ```

* 使用组合

    ```go
    /**
        queue.go
    */
    package queue

    type Queue []int
    
    func (q *Queue) Push(v int) {
    	*q = append(*q, v)
    }
    
    func (q *Queue) Pop() int {
    	head := (*q)[0]
    	*q = (*q)[1:]
    	return  head
    }
    
    func (q *Queue) IsEmpty() bool {
    	return len(*q) == 0
    }
    ```
    
    * 注意⚠️：每个目录是一个包，每个目录只能有一个包
    
    ```go
    /**
        entry.go 
    */
    package main

    import (
    	"fmt"
    	"learnGo/google工程师讲解go/object/queue"
    )
    
    func main()  {
    	q := queue.Queue{1}
    	q.Push(2)
    	q.Push(3)
    	fmt.Println(q.Pop())
    	fmt.Println(q.Pop())
    	fmt.Println(q.IsEmpty())
    	fmt.Println(q.Pop())
    
    	fmt.Println(q.IsEmpty())
    
    }
    ```