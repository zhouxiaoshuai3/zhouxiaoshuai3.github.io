---
layout: post
title: "GO 操作 MySQL 事务"
date: 2019-05-29
description: "GO操作MySQL事务"
tag: GO 日常练习
---


* Go语言MySQL事务应用包与函数：
    * `import ("github.com/jmoiron/sqlx")`
    * `conn, err := Db.Begin()`    开始事务
    * `conn.Commit() `             提交事务
    * `conn.Rollback()`            回滚事务

* 示例代码：

```go
package main

import (
	"fmt"
	_"github.com/go-sql-driver/mysql"
	"github.com/jmoiron/sqlx"
)


var Db *sqlx.DB

func init() {
	database, err := sqlx.Open("mysql", "root:zhou123!@tcp(127.0.0.1:3306)/oldboy")
	if err != nil {
		fmt.Println("connect fail ...", err)
		return
	}
	Db = database
}

func main() {
	conn, err := Db.Begin()
	if err != nil {
		fmt.Println("trasaction begin fail ...", err)
		return
	}
	data1, err := Db.Exec("insert into student (`name`, `sex`, `age`, `course`) " +
		"value (?, ?, ?, ?)", "李四", "女", 21, "java" )
	if err != nil {
		fmt.Println("insert first fail...", err)
		conn.Rollback()
		return
	}
	id, err := data1.LastInsertId()
	if err != nil {
		conn.Rollback()
		fmt.Println("insert first fail...", err)
		return
	}
	fmt.Println("insert succ id is ", id)
	data2, err := Db.Exec("insert into student (`name`, `sex`, `age`, `course`) " +
		"value (?, ?, ?, ?)", "王五", "男", 22, "python")
	if err != nil {
		fmt.Println("insert second fail...", err)
		conn.Rollback()
		return
	}
	id, err = data2.LastInsertId()
	if err != nil {
		conn.Rollback()
		fmt.Println("insert second fail...", err)
		return
	}
	fmt.Println("insert succ id is ", id)

	conn.Commit()
}

```    