---
url: /2018/10/20/design-patten-state.html
title: "设计模式(20)-状态模式"
keywords: "设计模式,状态模式"
description: "状态模式"
date: 2018-10-20T22:31:57+08:00
draft: false
tags: ["PHP", "设计模式"]
tags_weight: 100
categories: ["PHP", "设计模式"]
categoryes_weight: 100
---

[状态模式](https://github.com/wenjy/design_patten_php/blob/master/src/State.php)

## 简介

> 当一个对象的内在状态改变时允许改变其行为，这个对象看起来是改变了其类

> 状态模式主要解决的是当控制一个对象状态转换的条件表达式过于复杂时的情况，把状态的判断逻辑
转移到表示不同状态的一系列类当中，可以把复杂的判断逻辑简化

> 将与特定状态相关的行为局部化，并且将不同状态的行为分割开来

> 将特定的状态相关的行为都放入一个对象中，由于所有与状态相关的代码都存在于某个 State 子类中
所有通过定义新的子类可以很容易地增加新的状态和转换，可以消除庞大的条件分支语句

> 状态模式通过把各种状态转移逻辑分布到 State 子类之间，来减少相互依赖

> 当一个对象的行为取决于它的状态，并且它必须在运行时刻根据状态改变它的行为时，就可以考虑状态模式了

## 代码示例

```php
<?php
/**
 * 抽象状态类，定义一个接口以封装与 Context 的一个特定状态相关的行为
 *
 * Class State
 */
abstract class State
{
    abstract public function handle(Context $context);
}

/**
 * 具体状态，每一个子类实现一个与 Context 的一个状态相关的行为
 *
 * Class ConcreteStateA
 */
class ConcreteStateA extends State
{
    public function handle(Context $context)
    {
        // 设置下一状态
        $context->setState(new ConcreteStateB());
    }
}

class ConcreteStateB extends State
{
    public function handle(Context $context)
    {
        $context->setState(new ConcreteStateA());
    }
}

/**
 * 维护一个 State 子类的实例，这个实例定义当前的状态
 *
 * Class Context
 */
class Context
{
    private $state;

    /**
     * Context constructor.
     * @param $state
     */
    public function __construct(State $state)
    {
        $this->state = $state;
    }

    /**
     * @return mixed
     */
    public function getState()
    {
        return $this->state;
    }

    /**
     * @param mixed $state
     */
    public function setState($state)
    {
        $this->state = $state;
        echo '当前状态：' . get_class($this->state) . PHP_EOL;
    }

    public function request()
    {
        // 对请求做处理，并设置下一状态
        $this->state->handle($this);
    }

}

$c = new Context(new ConcreteStateA());

$c->request();
$c->request();
$c->request();
$c->request();
```
