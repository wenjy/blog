---
url: /2018/10/21/design-patten-strategy.html
title: "设计模式(21)-策略模式"
date: 2018-10-21T22:31:57+08:00
draft: false
tags: ["PHP", "DesignPatten"]
tags_weight: 100
categories: ["PHP"]
categoryes_weight: 100
---

[策略模式](https://github.com/wenjy/design_patten_php/blob/master/src/Strategy.php)

## 简介

```
它定义了算法家族，分别封装起来，让它们之间可以相互替换，
此模式让算法的变化，不会影响到使用算法的客户。

策略模式是一种定义一系列算法的方法，从概念上来看，所有的这些算法完成的都是相同的工作，只是实现不同，
它可以以相同的方式调用所有的算法，减少了各种算法类与使用算法类之间的耦合。

策略模式的 Strategy 类为 Context 定义了一系列的可供重用的算法或行为。继承有助于析取出这些算法中
公共的功能。

策略模式的优点是简化了单元测试，因为每个算法都有自己的类，可以通过自己的接口单独测试。

当不同的行为堆砌在一个类中时，就很难避免使用条件语句来选择合适的行为。将这些行为封装在一个个独立的
Strategy 类中，可以在使用这些行为的类中消除条件语句。

策略模式就是用来封装算法的，但是在实践中，我们发现可以用它来封装几乎任何类型的规则，只要在分析的过程
中听到需要在不同时间应用不同的业务规则，就可以考虑使用策略模式处理这种变化的可能性。
```

## 代码示例

```php
<?php
/**
 * 策略类，定义所有支持的算法的公共接口
 *
 * Class Strategy
 */
abstract class Strategy
{
    /**
     * 定义算法的接口
     *
     * @return mixed
     */
    public abstract function algorithmInterface();
}

/**
 * 具体策略类，封装了具体的算法或行为
 * Class StrategyA
 */
class StrategyA extends Strategy
{
    public function algorithmInterface()
    {
        echo '算法A实现' . PHP_EOL;
    }
}

class StrategyB extends Strategy
{
    public function algorithmInterface()
    {
        echo '算法B实现' . PHP_EOL;
    }
}

class StrategyC extends Strategy
{
    public function AlgorithmInterface()
    {
        echo '算法C实现' . PHP_EOL;
    }
}

/**
 * Class Context
 * 用一个contextInterface来配置，维护一个对Strategy对象的引用
 */
class Context
{
    /**
     * 保存具体的算法对象
     *
     * @var Strategy
     */
    private $strategy;

    /**
     * Context constructor.
     * @param $strategy
     */
    public function __construct($strategy)
    {
        $this->strategy = $strategy;
    }

    /**
     * 调用对应的子类方法
     *
     * @author jiangyi
     */
    public function contextInterface()
    {
        $this->strategy->algorithmInterface();
    }
}

// 此处也算是依赖注入
$context = new Context(new StrategyA());
$context->contextInterface();

/**
 * 结合简单工厂模式
 *
 */
class CashContext
{
    /**
     * @var Strategy
     */
    public $strategy;

    /**
     * CashContext constructor.
     * 构造函数里通过参数实例化对应的算法类，把注入的类整合为一个工厂
     * @param $type
     * @throws Exception
     */
    public function __construct($type)
    {
        switch (strtoupper($type)) {
            case 'A':
                $this->strategy = new StrategyA();
                break;
            case 'B':
                $this->strategy = new StrategyB();
                break;
            case 'C':
                $this->strategy = new StrategyC();
                break;
            default:
                throw new Exception('类型错误');
                break;
        }
    }

    /**
     * 调用对应的子类方法
     *
     * @author jiangyi
     */
    public function contextInterface()
    {
        $this->strategy->algorithmInterface();
    }
}

$cashContext = new CashContext('B');
$cashContext->contextInterface();

class MyContext
{
    /**
     * @var Strategy
     */
    public $strategy;

    /**
     * 可以把所有类型配置到一个数组中
     * @var array
     */
    protected $config = [
        'A' => StrategyA::class,
        'B' => StrategyB::class,
        'C' => StrategyC::class,
    ];

    /**
     * MyContext constructor.
     * @param $type
     * @throws Exception
     */
    public function __construct($type)
    {
        if (!isset($this->config[strtoupper($type)])) {
            throw new Exception('类型错误');
        }
        $this->strategy = new $this->config[strtoupper($type)];
    }

    /**
     * 调用对应的子类方法
     *
     * @author jiangyi
     */
    public function contextInterface()
    {
        $this->strategy->algorithmInterface();
    }
}

$cashContext = new MyContext('c');
$cashContext->contextInterface();
```
