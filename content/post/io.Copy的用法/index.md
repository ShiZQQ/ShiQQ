---
title: "Io.Copy的用法"
description: 
date: 2024-07-03T16:34:46+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - io.Copy
tags:
    - go
---


## 关于文件的定义

能读能写就是文件



### 那些可读可写

```
// File*
// stdout //控制太输出 
// stdin //控制台输入
// stderr //控制台报错
// bytes.Buffer
// net.Conn
```

### 关于net例子

~~~go

listen, err := net.Dial("tcp", "127.0.0.1:5000")
//listen.RemoteAddr().String()
open, _ := os.Open("./read.txt")
io.Copy(listen, open)
~~~



### 读文件

~~~ go
//Open只读
	open, _ := os.Open("./read.txt")

//第一个参数为文件路径,第二个参数控制文件的打开方式,第三个参数控制文件模式	
outFile, err := os.OpenFile("./out.txt", os.O_WRONLY|os.O_CREATE, 0755)

if err != nil {
		return
	}
//记得关了
	defer outFile.Close()

//缓冲读操作
reader := bufio.NewReader(open)
	for i := 0; i < 10; i++ {
		line, _, _ := reader.ReadLine()
		fmt.Println(string(line))
	}


//适合小文件
	all, err := io.ReadAll(open)
	fmt.Println(string(all), err)


//适合大文件，出现大文件一次读不完的情况，就用这种读法
	var buf = make([]byte, 1<<10)
	for {
		n, err := open.Read(buf)
		if n == 0 {
			break
		}
		if err != nil {
			return
		}
		fmt.Println(string(buf[:n]))
	}
~~~



## io.Copy

关于io.Copy，它可以操作任何可读可写的文件



### 使用方法

~~~go
io.Copy(目标文件, 源文件)
~~~



### 例子

```go
io.Copy(os.Stdout, open)

//创建一个文件
create, _ := os.Create("./tmp.txt")
//将读取的内容open写入文件create
io.Copy(create, open)
```