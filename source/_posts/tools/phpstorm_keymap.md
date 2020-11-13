---
url: /2020/05/12/phpstorm-keymap.html
title: "Phpstorm常用快捷键"
date: 2020-05-12T18:31:57+08:00
draft: false
tags: ["Phpstorm"]
tags_weight: 100
categories: ["Phpstorm"]
categoryes_weight: 100
---

本文参考：[peryiqiao https://learnku.com/laravel/t/5420/your-keyboard-shortcuts-please](https://learnku.com/laravel/t/5420/your-keyboard-shortcuts-please)

提取了一些自己常用的快捷键做记录

## Mac符号

符号|解释
---|---
⌘|Command
⇧|Shift
⌃|Control
↩|Enter/Return
⌥|Option/Alt

## 编辑

Win/Linux|Mac|注释
---|---|---
Ctrl + mouse over code|⌘+mouse over code|查看到简短的函数介绍
Alt + Insert|⌘N,⌃↩,⌃N|生成代码段（ 包括函数或类注释，版权信息，构造方法，抽象方法等），函数、类的注释我一般喜欢 `/**` + ↩
Ctrl + /|⌘/|以添加 “//” 的方式添加注释（单行注释）
Ctrl + Shift + /|⌘⌥/|添加 “/**/” 的方式添加注释（块注释），选中代码块，然后快捷键
Alt + Enter|⌥↩|显示意图行动。 Show Intention Action，自动use namespace、给私有属性生成getter setter函数
Ctrl + Alt + L|⌘⌥L|格式化代码，自动缩进
Tab / Shift + Tab|tab,⇧+tab|手动缩进 / 反向缩进
Ctrl + X or Shift + Delete|⌘X|剪切
Ctrl + C or Ctrl + Insert|⌘C|复制
Ctrl + V or Shift + Insert|⌘V|粘贴
Ctrl + Shift + V|⌘⇧V|从粘贴板中选择内容进行粘贴	
Ctrl + D|⌘D|将当前行或者选择的内容复制到下一行或光标处
Ctrl + Y|⌘del|删除光标所在的行，win上我改成了 Ctrl+Insert
Ctrl + Delete|⌥ + del|删除光标之后的部分单词
Ctrl + +/-|⌘ +,-|折叠 / 打开代码块，再次点击扩大折叠 / 打开范围
Ctrl + F4|⌘W|关闭当前页面，win上也被我设置成 Ctrl + W
Alt + Up/Down|⌘ + 上/下方向键|上下移动代码块、行，我自己设置的

## 搜索 / 替换

Win/Linux|Mac|注释
---|---|---
Ctrl + F|⌘F|查找、替换
Shift + Shift|⇧ + ⇧|可在全项目里搜索

## 导航相关
Win/Linux|Mac|注释
---|---|---
Ctrl + E|⌘E|打开最近打开过的文件列表
Ctrl + L|⌘L|跳转到行，有时根据报错日志可以快速定位到行
Ctrl + Click|⌘ + Click|跳转到函数的声明处
Alt + Left|⌥ + Left|返回从上次点击跳转到函数的声明处的地方
Ctrl + W|⌘ + W|关闭当前文件，自己设置的快捷键
Ctrl + Tab|⌘ + Tab|切换最近打开的文件

## 重构相关

重命名变量、类、函数，我一般都是用鼠标

