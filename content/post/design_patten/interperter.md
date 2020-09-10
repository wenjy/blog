---
url: /2018/10/12/design-patten-interpreter.html
title: "设计模式(12)-解释器模式"
keywords: "设计模式,解释器模式"
description: "解释器模式"
date: 2018-10-12T22:31:57+08:00
draft: false
tags: ["PHP", "设计模式"]
tags_weight: 100
categories: ["PHP", "设计模式"]
categoryes_weight: 100
---

[解释器模式](https://github.com/wenjy/design_patten_php/blob/master/src/Interpreter.php)

## 简介

```
给定一个语言，定义它的文法的一种表示，并定义一个解释器，
这个解释器使用该表示来解释语言中的句子

当有一个语言需要解释执行，并且你可以将该语言中的句子表示为一个抽象语法树时，可以使用解释器模式。
```

## 代码示例

```php
<?php
/**
 * 抽象表达式，声明一个抽象的解释操作，这个接口为抽象的语法树中所有的节点共享
 * Class AbstractExpression
 */
abstract class AbstractExpression
{
    abstract public function interpret(Context $context);
}

class TerminalExpression extends AbstractExpression
{
    public function interpret(Context $context)
    {
        echo '终端解释器' . PHP_EOL;
    }
}

class NonTerminalExpression extends AbstractExpression
{
    public function interpret(Context $context)
    {
        echo '非终端解释器' . PHP_EOL;
    }
}

/**
 * 包含解释器之外的一些全局信息
 *
 * Class Context
 */
class Context
{
    private $input;

    private $output;

    /**
     * @return mixed
     */
    public function getOutput()
    {
        return $this->output;
    }

    /**
     * @param mixed $output
     */
    public function setOutput($output)
    {
        $this->output = $output;
    }

    /**
     * @return mixed
     */
    public function getInput()
    {
        return $this->input;
    }

    /**
     * @param mixed $input
     */
    public function setInput($input)
    {
        $this->input = $input;
    }

}

$context = new Context();

$list[] = new NonTerminalExpression();
$list[] = new NonTerminalExpression();
$list[] = new TerminalExpression();

/* @var $v AbstractExpression */
foreach ($list as $v) {
    $v->interpret($context);
}
```
