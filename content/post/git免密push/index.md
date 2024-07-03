---
title: "Git免密push"
description: 
date: 2024-07-03T16:59:01+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - git
    
tags:
    - gitee
    - github
---

# 在忘记密码的情况下push或pull
在https连接的情况下push或pull会询问用户名和密码，每次输入用户密码会有点麻烦，所有我们可以使用SSH 来操作，公钥文件和私钥文件就成了我们免密验证的关键，以下是具体操作。

## 生成公钥文件和私钥文件

~~~bash
ssh-keygen -t rsa -C "2837355374@qq.com"
#`-t rsa`：指定生成的密钥类型是 RSA。RSA 是一种常见的公钥加密算法。#
#上面的邮箱是绑定的邮箱，本次我们拿gitee举例
~~~

输入指令之后点三次回车键

![image-20240703172400860](image-20240703172400860.png)

上面显示保存路径在 C:\Users\ShiQ/.ssh/id_rsa.

![image-20240703172556356](image-20240703172556356.png)

复制id_rsa.pub中的内容

## 配置gitee中SSH公钥

点击个人设置页面

![image-20240703174304243](image-20240703174304243.png)

点击ssh公钥

![image-20240703174342204](image-20240703174342204.png)

填写标题和公钥（刚才复制的内容）

输入gitee密码即可完成设置

![image-20240703174610562](image-20240703174610562.png)

再次pull代码时不会再询问用户名（邮箱）和密码

![image-20240703174816886](image-20240703174816886.png)
