---
url: /2018/10/07/design-patten-composite.html
title: "设计模式(7)-组合模式"
date: 2018-10-07T22:31:57+08:00
draft: false
tags: ["PHP", "DesignPatten"]
tags_weight: 100
categories: ["PHP", "DesignPatten"]
categoryes_weight: 100
---

[组合模式](https://github.com/wenjy/design_patten_php/blob/master/src/Composite.php)

## 简介

```
将对象组合成树形结构已表示部分-整体的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。

当你发现需求中是体现部分与整体层次的结构时，以及你希望用户可以忽略组合对象与单个对象的不同，
统一地使用组合结构中的所有对象时，就应该考虑使用组合模式了。

基本对象可以被组合成更复杂的组合对象，而这个组合对象又可以被组合，这样不断的递归下去
在客户代码中，任何用到基本对象的地方都可以使用组合对象了。

用户是不用关心到底是处理一个叶节点还是处理一个组合组件，也就是用不着为定义只而写一些选择判断语句了。

组合模式让客户可以一致的使用组合结构和单个对象。
```

## 代码示例

```php
<?php
/**
 * 组合中的对象声明接口，在适当的情况下，实现所有类共有接口的默认行为。
 * 声明一个接口用于访问和管理 Component 的子部件
 *
 * Class Component
 */
abstract class Component
{
    protected $name;

    /**
     * Component constructor.
     * @param $name
     */
    public function __construct($name)
    {
        $this->name = $name;
    }

    abstract public function add(Component $c);

    abstract public function remove(Component $c);

    abstract public function display($depth);

}

/**
 * 在组合中表示叶节点对象，叶节点没有子节点
 *
 * Class Leaf
 */
class Leaf extends Component
{

    /**
     * Leaf constructor.
     */
    public function __construct($name)
    {
        parent::__construct($name);
    }

    public function add(Component $c)
    {
        echo '不能添加' . PHP_EOL;
    }

    public function remove(Component $c)
    {
        echo '不能删除' . PHP_EOL;
    }

    public function display($depth)
    {
        echo str_repeat('-', $depth) . $this->name . PHP_EOL;
    }
}

/**
 * 定义有枝节点行为，用来存储子部件，在 Component 接口中实现与子部件有关的操作，
 * 比如增加、删除
 *
 * Class Composite
 */
class Composite extends Component
{
    /**
     * @var Component[]
     */
    private $list = [];

    /**
     * Composite constructor.
     */
    public function __construct($name)
    {
        parent::__construct($name);
    }

    public function add(Component $c)
    {
        $this->list[] = $c;
    }

    public function remove(Component $c)
    {
        $key = array_search($c, $this->list);
        if ($key !== false && array_key_exists($key, $this->list)) {
            unset($this->list[$key]);
        }
    }

    public function display($depth)
    {
        echo str_repeat('-', $depth) . $this->name . PHP_EOL;
        foreach ($this->list as $children) {
            $children->display($depth + 1);
        }
    }

}

$root = new Composite('root');
$root->add(new Leaf('leaf a'));
$root->add(new Leaf('left b'));

$comp = new Composite('x');
$comp->add(new Leaf('x a'));
$comp->add(new Leaf('x b'));

$root->add($comp);

$comp2 = new Composite('xy');
$comp2->add(new Leaf('xy a'));
$comp2->add(new Leaf('xy b'));

$root->add(new Leaf('leaf c'));
$leafd = new Leaf('leaf d');
$root->add($leafd);
$root->remove($leafd);

$root->add($comp2);
$root->display(0);
```
