---
title: "锁"
description: 
date: 2024-07-03T16:28:14+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - 互斥锁
    - redis
    - 数据库锁
tags:
    - go
    - redis
    - sync.Mutex
---


# 锁

关于锁是为安全考虑如若发生多线程同时操作的情况可以更安全的处理数据，类似于给同时的数据库操作排个序

## 关于锁的分类

读锁： 其他人可以一起读， 但是不能写
写锁： 其他人什么都干不了

## 关于数据库锁的操作

锁 Lock

~~~my
select * from user_moneys where id = 1 for update ;
~~~

加上for update后  在此语句commit提交之前需要等待，commit提交之后才能进行其他语句

~~~mysq
select *from user_moneys where id =1 for update;
select *from user_moneys where id= 2 for update;
~~~

~~~ my
select *from user_moneys where id =2 for update;
select *from user_moneys where id= 3 for update;
~~~

~~~my
select *from user_moneys where id =3 for update;
select *from user_moneys where id= 1 for update;
~~~

会形成死锁

#### 还有其他等方式（还没学）



## 互斥锁

~~~go
sync.Mutex
var lock sync.Mutex
~~~

单机的，只能在当前进程里（在不同线程{协程}中上锁）用的锁,保证协程中的数据

### 例子

~~~go
func Incr() {
	//var tmp = 0
	for i := 0; i < 100000; i++ {
		lock.Lock()
		mp[0] = mp[0] + 1
		lock.Unlock()
		//tmp += 1
		//count += 1
	}
	//count += tmp
	wg.Done()
}
func main2() {
	begin := time.Now()
	var n = 50
	wg.Add(n)
	for i := 0; i < n; i++ {
		go Incr()
	}
	wg.Wait()
	// 10000 * 5
	fmt.Println(mp, time.Now().Sub(begin))

}
~~~

也可以这样,稍微快点

~~~go

func Incr() {
	//var tmp = 0
    lock.Lock()
	for i := 0; i < 100000; i++ {
		mp[0] = mp[0] + 1
		//tmp += 1
		//count += 1
	}
    lock.Unlock()
	//count += tmp
	wg.Done()
}
func main2() {
	begin := time.Now()
	var n = 50
	wg.Add(n)
	for i := 0; i < n; i++ {
		go Incr()
	}
	wg.Wait()
	// 10000 * 5
	fmt.Println(mp, time.Now().Sub(begin))

}
~~~

保证了单个进程中的数据



### 单纯的上下锁并不能保证数据的安全性

~~~go
// sync.Mutex
var lock sync.Mutex
var lock_map = map[string]*sync.Mutex{}

//安全上锁
func lockByName(name string) {

	if _, exists := lock_map[name]; !exists {
		lock.Lock()
		if _, exists := lock_map[name]; !exists {
			lock_map[name] = &sync.Mutex{}
		}
		lock.Unlock()
	}
	lock_map[name].Lock()

}

//下锁
func unlockByName(name string) {
	lock_map[name].Unlock() // 释放 name 对应的锁
}
~~~





## 分布式锁

面试问的比较多，有redis（面试常问）、etcd。



## redis

redis可以在不同进程中上锁，而单机锁只能在进程中的不同线程（协程）中上锁

```txt
1. 单线程 所以不存在多线程并发问题
2. 大致可以理解成是一个哈希表（map<string,void*>）
3. 内存数据库， 所有数据存在内存中
```

### 常被用于

```txt
1. 分布式锁
2. 缓存
```

### 关于操作命令

```redis
### keys *  //查看所有已经创立的键
### set key value //创建一个键，value为键值内容
### set x 1 ex（expire） 1 //创建一个名为“下”的键，键值为1，此键1秒后过期（ex的单位是秒）
### incr key  //key+=1
### decr key  //key-=1
### setnx key value //插入一个键和键值，如果此键已存在则返回0，可用于上锁操作  
### del key //删除这个键
### get key //获取键值
### publish channe //向一个频道发送消息（如果没人监听则发送失败）
### subscribe channe  //监听一个频道
### lpush key value  //类似于链表，在链表（key）的左边插入一个值
### rpush key value  //同理，向右边插入一个值
### lrange key 0 -1 //查看链表全部，-1是链表最后的位置
```



### 具体操作例子

~~~go

client := redis.NewClient(&redis.Options{
		//Addr:                       "127.0.0.1:6379",
	})


//setnx一个键为x，键值为1，1分钟后过期
x := client.SetNX(context.Background(), "x", "1", time.Minute)

for {
		x := client.SetNX(context.Background(), "x", "1", time.Minute)
		//fmt.Println(x.Val())
		if x.Val() {
			break
		}
		time.Sleep(time.Millisecond * 50)
	}
	fmt.Println("上锁成功")
	client.Del(context.Background(), "x")


//监听
subscribe := client.Subscribe(context.Background(), "ch1")
channel := subscribe.Channel()
~~~
