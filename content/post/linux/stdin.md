---
url: /2020/06/07/linux-stdin.html
title: "Linux 输出重定向"
keywords: "Linux,输出重定向"
description: "Linux输出重定向"
date: 2020-06-07T19:29:11+08:00
draft: false
tags: ["Linux"]
tags_weight: 100
categories: ["Linux"]
categoryes_weight: 100
---

Linux 一切皆文件

## 标准输入、输出、错误

Linux中有三种标准输入输出，分别是`STDIN`，`STDOUT`，`STDERR`，对应的数字是0，1，2。

`STDIN`是标准输入，默认从键盘读取信息；

`STDOUT`是标准输出，默认将输出结果输出至终端；

`STDERR`是标准错误，默认将输出结果输出至终端。

而一般默认

`STDIN` 打开 /dev/stdin 也就是标准输入

`STDOUT` 打开 /dev/stdout 也就是标准输出

`STDERR` 打开 /dev/stderr 也就是标准错误

所以一般使用的时候，用 0,1,2代替

## 重定向命令

重定向命令`>`就是改变fd所对应的指针

常用的重定向命令

```
>/dev/null 2>&1 // 会将标准输出，错误输出都重定向至/dev/null，也就是全部丢弃

// 首先将fd1的文件指针更改为指向/dev/null
// 然后将fd2的文件指针更改为fd1所对应的文件指针，也就是/dev/null文件

```

验证示例：
```
touch exists.log
ll exists.log not_exists.log
// 输出
// ls: cannot access not_exists.log: No such file or directory
// -rw-rw-r-- 1 www www 0 Jun  8 15:01 exists.log

ll exists.log not_exists.log >/dev/null 2>&1
```
结果我们看到什么输出都没有

`/dev/null`在类Unix系统中称空设备，是一个特殊的设备文件，它丢弃一切写入其中的数据（但报告写入操作成功），读取它则会立即得到一个EOF。

```
2>&1 >/dev/null // 只会将标准输出重定向至/dev/null

// 首先将fd2的文件指针更改为fd1所对应的文件指针，也就是STDOUT文件
// 然后将fd1的文件指针更改为指向/dev/null

```

验证示例：
```
touch exists.log
ll exists.log not_exists.log 2>&1 >/dev/null
// 只输出错误
// ls: cannot access not_exists.log: No such file or directory
```
