---
url: /2020/05/16/mysql-lock-transaction.html
title: "MySQL 锁和事务机制"
keywords: "MySQL锁机制,事务机制"
description: "MySQL锁机制,事务机制"
date: 2020-05-16T21:31:57+08:00
draft: true
tags: ["MySQL"]
tags_weight: 100
categories: ["MySQL"]
categoryes_weight: 100
---

## 锁简介

想要控制并发读写，就离不开锁
在处理并发读或者写时，可以通过实现一个由两种类型锁组成的锁系统来解决问题。
这两种类型的锁通常被称为共享锁（shared lock）和排他锁（exclusive lock），也叫读锁（read lock）和写锁（write lock）.

读锁是共享的，互不阻塞的；写锁是排他的，一个写锁会阻塞其他的写锁和读锁；这样才能确保在给定的时间只有一个用户能执行写入，
并防止其他客户读取正在写入的同一资源。

MySQL最重要的两种锁策略：

1. 表锁（table lock）

表锁是MySQL中最基本的锁策略，并且是开销最小的的策略

2. 行锁（row lock）
