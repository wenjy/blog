---
url: /2018/10/02/design-patten-adapter.html
title: "设计模式(2)-适配器模式"
date: 2018-10-02T22:31:57+08:00
draft: false
tags: ["PHP", "DesignPatten"]
tags_weight: 100
categories: ["PHP", "DesignPatten"]
categoryes_weight: 100
---

[适配器模式](https://github.com/wenjy/design_patten_php/blob/master/src/Adapter.php)

## 简介

```
将一个类的接口转换成客户所希望的另外的接口。 Adapter 模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

系统的数据和行为都正确，但是接口不符合时，我们应该考虑用适配器，目的是使控制范围之外的一个原有对象与某个接口匹配。
适配器模式主要应用于希望复用一些现存的类，但是接口又与复用环境要求不一致的情况，比如在需要对早期代码复用一些功能等应用上很有实际价值。

想使用一个已经存在的类，但是它的接口，也就是它的方法和你的要求不相同时，就应该考虑用适配器模式。
或者说两个类所做的事情相同或相似，但是具有 不同的接口时要使用它

在双方都不太容易修改的时候再使用适配器模式

```

## 代码示例

```php
<?php
/**
 * 这个是我们所需要的东西，但是它的接口和普通的接口不一致
 *
 * Class Adaptee
 */
class Adaptee
{
    public function specificRequest()
    {
        echo '特殊请求' . PHP_EOL;
    }
}

/**
 * 这是一个普通的接口
 *
 * Class Target
 */
class Target
{
    public function request()
    {
        echo '普通请求' . PHP_EOL;
    }
}

/**
 * 我们定义一个新的类 Adapter 继承 Target
 * 并且复写 request() 方法，使之实际调用的是 Adaptee 的方法，这样就保持了接口的一致
 * Class Adapter
 */
class Adapter extends Target
{
    /**
     * @var Adaptee
     */
    private $adaptee;

    public function __construct()
    {
        $this->adaptee = new Adaptee();
    }

    public function request()
    {
        $this->adaptee->specificRequest();
    }
}

$target = new Adapter();
$target->request();
```
