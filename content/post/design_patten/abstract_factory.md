---
url: /2018/10/01/design-patten-abstract-factory.html
title: "设计模式(1)-抽象工厂模式"
date: 2018-10-01T22:31:57+08:00
draft: false
tags: ["PHP", "DesignPatten"]
tags_weight: 100
categories: ["PHP", "DesignPatten"]
categoryes_weight: 100
---

[抽象工厂模式](https://github.com/wenjy/design_patten_php/blob/master/src/AbstractFactory.php)

## 简介

```
提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类
```

## 代码示例

```php
<?php
interface IDepartment
{
    public function insert(Department $department);

    public function getDepartment($id);
}

class MySqlDepartment implements IDepartment
{
    public function insert(Department $department)
    {
        echo 'mysql 新增记录' . PHP_EOL;
    }

    public function getDepartment($id)
    {
        echo 'mysql 查询一条记录' . PHP_EOL;
    }
}

class SqlServerDepartment implements IDepartment
{
    public function insert(Department $department)
    {
        echo 'sqlServer 新增记录' . PHP_EOL;
    }

    public function getDepartment($id)
    {
        echo 'sqlServer 查询一条记录' . PHP_EOL;
    }
}

interface InterfaceFactory
{
    public function createDepartment();
}

class SqlServerFactory implements InterfaceFactory
{
    public function createDepartment()
    {
        return new SqlServerDepartment();
    }
}

class MySqlFactory implements InterfaceFactory
{
    public function createDepartment()
    {
        return new MySqlDepartment();
    }
}

class Department
{

}

$department = new Department();

$iDepartment = (new MySqlFactory())->createDepartment();
$iDepartment->insert($department);
$iDepartment->getDepartment(1);

$iDepartment = (new SqlServerFactory())->createDepartment();
$iDepartment->insert($department);
$iDepartment->getDepartment(1);
```
