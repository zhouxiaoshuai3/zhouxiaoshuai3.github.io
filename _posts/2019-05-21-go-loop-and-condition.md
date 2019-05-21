---
layout: post
title: "GO语言中的循环和条件语句"
date: 2019-05-21 
description: "GO语言中的循环和条件语句"
tag: GO 从入门到实战
--- 

#### 循环：
* 与其他主要编程语言的差异
    * Go 语言仅支持循环关键字 `for`
    * 在声明 循环 语句时，`for` 不用使用 `()`
    
    * 代码示例1:
    ```go
    for j := 7; j <= 9; j++ {
        ...
    }
    ```
    
    * 代码示例2:
    
        ```go
        package loo_test
    
        import (
        	"fmt"
        	"testing"
        )
        
        func TestLoop(t *testing.T)  {
        	// while 条件循环
        	// while (n < 5)
        	n := 0
        	for n < 5 {
        		n ++
        		fmt.Println(n)
        	}
        }
        ```
    * 代码示例3:
    
        ```go
        // 无限循环
        n := 0
        for {
            ...
        }
        ```
        
*  `while` 的使用方法：
    
    ```go
    package loo_test

    import (
    	"testing"
    )
    
    func TestLoop(t *testing.T)  {
    	n := 0
    	for n < 5 {
    		t.Log(n)
    		n++
    	}
    }
    /**
  loop_test.go:10: 0
  loop_test.go:10: 1
  loop_test.go:10: 2
  loop_test.go:10: 3
  loop_test.go:10: 4
 */
    ```
    
#### 条件语句：
  
#### `if` 条件控制语句 ；
  
* 与其他主要编程语言的差异
    * condition 表达式结果必须为布尔值；
    * 支持变量赋值； if 语句中可以分为两段，第一段可以是赋值；[主要作用 类似 函数可以有多个返回值]

    ```go
    if var declaration; condition {
        // code to be executed if condition is true
    }
    ```    
    
    * 通常用在判断函数的多返回值，第一个返回值是实际使用的 值 ，第二个返回值则可以判断是否有错误；
    * 代码示例4:

    ```go
    package loo_test
    import "testing"
    
    func TestIfMultiSec(t *testing.T)  {
    	if a := true; a {
    		t.Log(a)
    	}
    }
    /**
    === RUN   TestIfMultiSec
    --- PASS: TestIfMultiSec (0.00s)
        condition_test.go:6: true
    PASS
    */
    ```
    
#### `switch` 条件控制语句 ；
    
* 条件表达式不限制为常量或者整数；
* 单个 `case` 中，可以出现多个结果选项，使用逗号分隔；
* Go 语言中不需要使用 `break` 来明确推出一个 `case`；
* 可以不设定 switch 之后的条件表达式，在此情况下，整个 switch 结构与 多个 `if ... else ...` 的逻辑作用等同；

    * 代码示例5:

    ```go
    package loo_test
    
    import (
    	"fmt"
    	"runtime"
    	"testing"
    )
    
    func TestSwitch(t *testing.T)  {
    	switch os := runtime.GOOS; os {
    	case "darwin" :
    		fmt.Println("OS X")
    	case "linux":
    		fmt.Printf("Linux")
    	default:
    		fmt.Printf("other ...")
    	}
    }
    ```

* 代码示例 6:
    * 可以在 case 后面支持多个选项
    
    ```go
    package loo_test

    import (
    	"fmt"
    	"runtime"
    	"testing"
    )
    
    func TestSwitchMultiSe(t *testing.T) {
    	for i := 0; i < 5; i++ {
    		switch i {
    		case 0, 2:
    			fmt.Println(i, "偶数")
    		case 1, 3:
    			fmt.Println(i, "奇数")
    		default:
    			fmt.Println("it is not in 0~3")
    		}
    	}
    }
    /**
    === RUN   TestSwitchMultiSe
    0 偶数
    1 奇数
    2 偶数
    3 奇数
    it is not in 0~3
    --- PASS: TestSwitchMultiSe (0.00s)
    PASS
     */
    ```    
    
    * 可以在 case 中使用表达式：

    ```go
    
    func TestSwitchCondition(t *testing.T)  {
    	for i := 0; i < 5; i++ {
    		switch {
    		case i%2 == 0:
    			t.Log(i, "偶数")
    		case i%2 == 1:
    			t.Log(i, "奇数")
    		default:
    			t.Log("unkwon")
    		}
    	}
    }
    /**
      switch_condition_test.go:37: 0 偶数
      switch_condition_test.go:39: 1 奇数
      switch_condition_test.go:37: 2 偶数
      switch_condition_test.go:39: 3 奇数
      switch_condition_test.go:37: 4 偶数
     */
    ```