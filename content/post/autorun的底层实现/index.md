---
title: "Autorun的底层实现"
description: 
date: 2024-07-03T16:40:27+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - autorun
tags:
    - react
---

# autorun的底层实现

```js
const map = new Map()
const obj = {}
let currentFunc = null
const p = new Proxy(obj, {
    get(target, p, receiver) {
    //实现依赖收集
        if (currentFunc) {
            const s = map.get(p) || new Set()
            s.add(currentFunc)
            map.set(p,s)


            // map.set(p, [...(map.get(p) || []), currentFunc])
        }
        // console.log('我的', p, '属性 被读取')
        return target[p]
    },
    set(target, p, newValue, receiver) {
        target[p] = newValue
        //取出map中的监听的东西，如果改变则重新跑一边存的函数
        const callback = map.get(p);
        // console.log(callback)
        if (callback) {
            callback.forEach(fn => fn())
        }
        return true
    }
})


const autorun = (fn) => {

//保存函数，就是() => {
 //   p.id
   // console.log('监听到p.id变化', p.id)
//}
    currentFunc = fn
    fn()
    currentFunc = null
    // 1. 收集依赖
    // 2. 监听这些依赖
}

autorun(() => {
    p.id
    console.log('监听到p.id变化', p.id)
})
autorun(() => {
    p.id
    console.log('监听到p.id变化 2', p.id)
})

setInterval(() => {
    p.id = (p.id || 0) + 1
    // console.log(p.id)
}, 1000)
```