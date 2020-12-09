---
url: /2018/10/19/design-patten-singleton.html
title: "设计模式(19)-单例模式"
keywords: "设计模式,单例模式"
description: "单例模式"
date: 2018-10-19T22:31:57+08:00
draft: false
tags: ["PHP", "设计模式"]
tags_weight: 100
categories: ["PHP", "设计模式"]
categoryes_weight: 100
---

[单例模式](https://github.com/wenjy/design_patten_php/blob/master/src/Singleton.php)

## 简介

> 保证一个类仅有一个实例，并提供一个访问它的全局访问点

> 通常我们可以让一个全局变量使得一个对象被访问，但它不能防止你实例化多个对象。
一个最好的办法就是，让类自身保存它的唯一实例。这个类可以保证没有其他实例可以被创建，
并且它可以提供一个访问该实例的方法。

## 代码示例

```php
<?php
class Singleton
{
    private static $instance;

    private function __construct()
    {
    }

    private function __clone()
    {
    }

    public static function getInstance()
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }
}

$s1 = Singleton::getInstance();
$s2 = Singleton::getInstance();

if ($s1 === $s2) {
    echo '一样的';
}
```
