---
url: /2018/10/20/design-patten-state-work.html
title: "设计模式(20)-状态模式2"
date: 2018-10-20T22:31:57+08:00
draft: false
tags: ["PHP", "DesignPatten"]
tags_weight: 100
categories: ["PHP"]
categoryes_weight: 100
---

[状态模式2](https://github.com/wenjy/design_patten_php/blob/master/src/StateWork.php)

## 简介

```
简单实现一个表示工作状态的类。
```

## 代码示例

```php
<?php
abstract class StateWork
{
    abstract public function writeProgram(Work $w);
}

class ForenoonState extends StateWork
{
    public function writeProgram(Work $w)
    {
        if ($w->getHour() < 12) {
            echo '当前时间：' . $w->getHour() . '点，上午工作，精神百倍';
        } else {
            $w->setState(new NoonState());
            $w->writeProgram();
        }
    }
}

class NoonState extends StateWork
{
    public function writeProgram(Work $w)
    {
        if ($w->getHour() < 13) {
            echo '当前时间：' . $w->getHour() . '点，午饭，午休';
        } else {
            $w->setState(new AfternoonState());
            $w->writeProgram();
        }
    }
}

class AfternoonState extends StateWork
{
    public function writeProgram(Work $w)
    {
        if ($w->getHour() < 17) {
            echo '当前时间：' . $w->getHour() . '点，下午，继续工作';
        } else {
            $w->setState(new EveningState());
            $w->writeProgram();
        }
    }
}

class EveningState extends StateWork
{
    public function writeProgram(Work $w)
    {
        if ($w->getFinish()) {
            $w->setState(new RestState());
            $w->writeProgram();
        } else {
            if ($w->getHour() < 21) {
                echo '当前时间：' . $w->getHour() . '点，加班，好累';
            } else {
                $w->setState(new SleepingState());
                $w->writeProgram();
            }
        }
    }
}

class SleepingState extends StateWork
{
    public function writeProgram(Work $w)
    {
        echo '当前时间：' . $w->getHour() . '点，洗澡，睡觉';
    }
}

class RestState extends StateWork
{
    public function writeProgram(Work $w)
    {
        echo '当前时间：' . $w->getHour() . '点，下班，下班';
    }
}

class Work
{
    private $current;
    private $hour;
    private $finish;

    /**
     * Work constructor.
     */
    public function __construct()
    {
        $this->current = new ForenoonState();
    }

    /**
     * @return mixed
     */
    public function getCurrent()
    {
        return $this->current;
    }

    /**
     * @param mixed $current
     */
    public function setCurrent($current)
    {
        $this->current = $current;
    }

    /**
     * @return mixed
     */
    public function getHour()
    {
        return $this->hour;
    }

    /**
     * @param mixed $hour
     */
    public function setHour($hour)
    {
        $this->hour = $hour;
    }

    /**
     * @return mixed
     */
    public function getFinish()
    {
        return $this->finish;
    }

    /**
     * @param mixed $finish
     */
    public function setFinish($finish)
    {
        $this->finish = $finish;
    }

    public function setState(StateWork $s)
    {
        $this->current = $s;
    }

    public function writeProgram()
    {
        $this->current->writeProgram($this);
    }
}

$work = new Work();
$work->setHour(22);
$work->writeProgram();
```
