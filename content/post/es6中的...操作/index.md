---
title: "Es6中的...操作"
description: 
date: 2024-07-03T16:42:26+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - es6
tags:
    - list
---

# es6中的...

~~~ js
//在这里...有一个组装的作用，把多余的参数组装成一个[]
const fn = (a, b, c, ...d) => {
    console.log(a, b, c, d)
}
// fn(1, 1, 1, 21, 22, 2, 22, [1, 2, 3, 3])
//在这里...有一个拆解[]的作用，把[1, 2, 3, 3, 3, 3, 3, 3, 3,]一个一个的传进去
fn(1, 2, 3, 8, 4, 2, 1, ...[1, 2, 3, 3, 3, 3, 3, 3, 3,])
// li = [1, 2, 3]
fn([1, 2, 3])
// li = [1, 2, 3, [1, 2, 3]]
fn(...[1, 2, 3])

//输出
1 2 3 [
  8, 4, 2, 1, 1, 2,
  3, 3, 3, 3, 3, 3,
  3
]
[ 1, 2, 3 ] undefined undefined []
1 2 3 []

~~~





~~~ js
const li1 = [1, 2, 3]
const li2 = [2, 3, 4]
const li3 = ['a', 'b', 'c']

//把上面的数组展开合并并使用set去重
const li = new Set([...li1, ...li2, ...li3])
console.log(li)
//输出
Set(7) { 1, 2, 3, 4, 'a', 'b', 'c' }




const li1 = [1, 2, 3]
const li2 = [2, 3, 4]
const li3 = ['a', 'b', 'c']

//把上面的数组展开合并并使用set去重
const li = [...new Set([...li1, ...li2, ...li3])]
console.log(li)

//输出
//set展开后的数组
 [1, 2, 3, 4, 'a', 'b', 'c' ]
~~~
