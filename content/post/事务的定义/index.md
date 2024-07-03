---
title: "事务的定义"
description: 
date: 2024-07-03T16:32:48+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - 事务
---

# 事务

把多操作绑定在一起，如果一个操作失败则全部操作失败，数据回滚。

### 例子

~~~go
db.Transaction(func(tx *gorm.DB) error {
  // 在事务中执行一些 db 操作（从这里开始，您应该使用 'tx' 而不是 'db'）
  if err := tx.Create(&Animal{Name: "Giraffe"}).Error; err != nil {
    // 返回任何错误都会回滚事务
    return err
  }
~~~
