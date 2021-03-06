---
url: /2020/03/29/linux-file-permission.html
title: "Linux文件目录权限"
keywords: "Linux,文件目录权限"
description: "Linux文件目录权限"
date: 2020-03-29T19:29:11+08:00
draft: false
tags: ["Linux"]
tags_weight: 100
categories: ["Linux"]
categoryes_weight: 100
---

Linux一切皆文件

## 权限解释

首先我们执行 `ls -al /etc/passwd` 看一下 passwd文件的权限如下：

权限|连接|所有者|用户组|文件大小|修改日期|文件名
---|---|---|---|---|---|---
-rw-r--r--|1|root|wheel|6393|Jul 31  2016|/etc/passwd

第一个字符代表这个文件是目录(`d`)、文件(`-`)或者链接文件(`l`)等

接下来三组文件权限9个字符，均为`rwx`三个参数组合，代表读/写/执行权限，分别属于文件所有者、用户组、其他人的权限。

```
一般记r权限为数字4，w为数字2，x为数字7

所以上面passwd文件的权限也可以说为 644，4+2+0 4+0+0 4+0+0
所有者拥有读写权限，用户组和其他人拥有读权限
```

`文件和目录的权限意义并不相同`

### 对文件而言

`r`(read)权限代表可读取此文件内容

`w`(write)可编辑文件内容（**不包含删除该文件**）

`x`(execute)可被系统执行

### 对目录而言

`r`(read contents in directory)具有读取目录结构列表的权限，如使用`ls`命令读取文件夹下的文件列表

`w`(modify contents in directory)这个权限对目录来说是很强大的，因为它表示你具有更改该目录结构列表的权限，具体如下：

- 新建文件与目录，如`touch`，`mkdir`等命令
- 删除已存在的文件与目录（不论该文件的权限为何）如`rm`等命令
- 将已存在的文件重命名，如`mv`等命令
- 转移该目录内的文件，目录位置

`x`(access directory)代表用户能否进入该目录成为工作目录(work directory)，如`cd`等命令

## 文件的默认权限：umask

也就是新建文件或目录的默认权限，`umask` `umask -S`命令查看，一般为0022
若用户创建文件，默认没有执行权限，则总权限为`-rw-rw-rw-`
若用户新建目录，则默认全部权限`drwxrwxrwx`
umask的分数指的是，该默认值需要减到的权限，rwx为421

对上面的上umask来说
用户创建文件：(666 - 022) = 644 = -rw-r--r--
用户创建目录：(777 - 022) = 755 = drwxr-xr-x

## 如何改变文件用户权限

当我们需要改变文件(目录)权限时，有两个命令`chown`，`chmod`

参考文献：

《鸟哥的Linux私房菜》



