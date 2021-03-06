---
url: /2018/10/10/design-patten-factory-method.html
title: "设计模式(10)-工厂方法模式"
keywords: "设计模式,工厂方法模式"
description: "工厂方法模式"
date: 2018-10-10T22:31:57+08:00
draft: false
tags: ["PHP", "设计模式"]
tags_weight: 100
categories: ["PHP", "设计模式"]
categoryes_weight: 100
---

[工厂方法模式](https://github.com/wenjy/design_patten_php/blob/master/src/FactoryMethod.php)

## 简介

> 定义一个用于创建对象的接口，让子类决定实例化哪一个类，工厂方法使一个类的实例化延迟到子类。

> 工厂方法克服了简单工厂违背了开放-封闭原则的缺点，又保持了封装对象创建过程的优点。
工厂方法模式是简单工厂的进一步抽象和推广

## 代码示例

```php
<?php
class LeiFeng
{
    public function sweep()
    {
        echo '扫地' . PHP_EOL;
    }

    public function wash()
    {
        echo '洗衣' . PHP_EOL;
    }

    public function buyRice()
    {
        echo '买米' . PHP_EOL;
    }
}

/**
 * 雷锋的继承者
 * 也可以有更多的方法
 *
 * Class Undergraduate
 */
class Undergraduate extends LeiFeng
{
    public function sweep()
    {
        parent::sweep();
        echo '扫地后休息一会' . PHP_EOL;
    }

}

class Volunteer extends LeiFeng
{

}

/**
 * 创建雷锋的接口
 *
 * Interface IFactory
 */
interface IFactory
{
    public function createLeiFeng();
}

/**
 * 实现创建雷锋
 *
 * Class UndergraduateFactory
 */
class UndergraduateFactory implements IFactory
{
    public function createLeiFeng()
    {
        return new Undergraduate();
    }
}

class VolunteerFactory implements IFactory
{
    public function createLeiFeng()
    {
        return new Volunteer();
    }
}

$factory = new UndergraduateFactory();
$student = $factory->createLeiFeng();

$student->buyRice();
$student->sweep();
$student->wash();
```
