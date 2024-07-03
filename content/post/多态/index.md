---
title: "多态"
description: 
date: 2024-07-03T16:38:27+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - 多态
    - 示例
---

# 多态

类似于函数不管传入的参数类型是什么，参数只要满足特定的条件就可以传入j

```go
package main

import "fmt"

type Animal struct {
}

type Person struct {


//interface是描述需要的条件（其中的条件必须是方法）
type Sayer interface {
    Say()
    Run()
}

func Run(s Sayer) {

}

func (a Animal) Say() {

}
func (a Person) Say() {

}

type Integer int

func (x Integer) Say() {

}

func main() {
    var a = Animal{}
    var person = Person{}

    Run(a)
    Run(person)
    var x Integer = 1
    var y = 1
    fmt.Println(int(x) == y)
    Run(x)
}
```
