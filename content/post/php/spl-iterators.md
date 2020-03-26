---
url: /2017/12/20/php-spl-iterators.html
title: "PHP Spl Iterators"
date: 2017-12-20T14:53:12+08:00
draft: false
tags: ["PHP"]
tags_weight: 100
categories: ["PHP"]
categoryes_weight: 100
---
[PHP文档](https://www.php.net/manual/zh/spl.iterators.php)

```
PHP SPL 提供一系列迭代器以遍历不同的对象，有些比较实用（如ArrayAccess），有些比较鸡肋，从来没有使用过。
```

## Iterator

实现一个最简单的迭代器接口

```php
<?php
class MyIterator implements Iterator
{
    private $array = [];

    private $valid = false;

    public function __construct(array $array)
    {
        $this->array = $array;
    }

    /**
     * 返回到迭代器的第一个元素
     * @author jiangyi
     */
    function rewind()
    {
        $this->valid = (false !== reset($this->array));
    }

    /**
     * 返回当前元素
     * @return mixed
     * @author jiangyi
     */
    function current()
    {
        return current($this->array);
    }

    /**
     * 返回当前元素的键
     * @return int
     * @author jiangyi
     */
    function key()
    {
        return key($this->array);
    }

    /**
     * 向前移动到下一个元素
     * @author jiangyi
     */
    function next()
    {
        $this->valid = (false !== next($this->array));
    }

    /**
     * 检查当前位置是否有效
     * @return bool
     * @author jiangyi
     */
    function valid()
    {
        return $this->valid;
    }
}

$array = [
    'a' => "firstelement",
    "secondelement",
    "lastelement",
];
$it = new MyIterator($array);

foreach ($it as $key => $value) {
    echo "{$key}=>{$value}\n";
}

$it->rewind();
while ($it->valid()) {
    echo "{$it->key()}=>{$it->current()}\n";
    $it->next();
}
```

## Countable

实现统计接口

```php
<?php
class MyCountable implements Countable
{
    private $myCount = 3;

    public function count()
    {
        return $this->myCount;
    }
}

$countable = new MyCountable();
var_dump($countable->count());
var_dump(count($countable));
```

## AppendIterator

`这个迭代器可以遍历很多的迭代器`

用在什么地方呢？暂时没想到

```php
<?php

$arrayIteratorOne = new ArrayIterator(['a', 'b', 'c']);
$arrayIteratorTwo = new ArrayIterator(['d', 'e', 'f']);

$iterator = new AppendIterator;
$iterator->append($arrayIteratorOne);
$iterator->append($arrayIteratorTwo);

foreach ($iterator as $current) {
    echo $current . PHP_EOL;
}

// 输出a b c d e f
```

## ArrayAccess

`提供像访问数组一样访问对象的能力的接口`

大部分框架的model都实现它来达到上述的效果

```php
<?php

class MyArrayAccess implements ArrayAccess, IteratorAggregate
{
    private $container = [];
    public $title;
    public $author;
    public $category;

    public function __construct($title, $author, $category)
    {
        $this->container = [
            "one"   => 1,
            "two"   => 2,
            "three" => 3,
        ];
        $this->title = $title;
        $this->author = $author;
        $this->category = $category;
    }

    /**
     * 设置一个偏移位置的值
     * @param mixed $offset
     * @param mixed $value
     * @author jiangyi
     */
    public function offsetSet($offset, $value)
    {
        $this->{$offset} = $value;
    }

    /**
     * 检查一个偏移位置是否存在
     * @param mixed $offset
     * @return bool
     * @author jiangyi
     */
    public function offsetExists($offset)
    {
        return isset($this->{$offset});
    }

    /**
     * 复位一个偏移位置的值
     * @param mixed $offset
     * @author jiangyi
     */
    public function offsetUnset($offset)
    {
        unset($this->{$offset});
    }

    /**
     * 获取一个偏移位置的值
     * @param mixed $offset
     * @return mixed|null
     * @author jiangyi
     */
    public function offsetGet($offset)
    {
        return isset($this->{$offset}) ? $this->{$offset} : null;
    }

    function getIterator()
    {
        return new ArrayIterator($this);
    }
}

$obj = new MyArrayAccess('a', 'wen', 'new');

var_dump(isset($obj['title']));
var_dump($obj["title"]);
unset($obj["title"]);
var_dump(isset($obj["title"]));
$obj["title"] = "A value";
var_dump($obj["title"]);
$obj['a'] = 'Append 1';
$obj['b'] = 'Append 2';
$obj['c'] = 'Append 3';

foreach ($obj as $field => $value) {
    echo "$field=>$value\n";
}
```

## ArrayIterator

`这个迭代器允许在遍历数组和对象时删除和更新值与键`

其实它实现了ArrayAccess , SeekableIterator , Countable , Serializable拥有更多的功能

```php
<?php

$array = ['a', 'b', 'c', 'd'];

try {
    $object = new ArrayIterator($array);
    foreach ($object as $key => $value) {
        echo $key . ' => ' . $value . PHP_EOL;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

## ArrayObject

更像手动的foreach

```php
<?php

$array = ['a', 'b', 'c', 'd'];

$arrayObj = new ArrayObject($array);
$arrayObj->append('e');
$arrayObj->natcasesort();
echo 'count->' . $arrayObj->count() . PHP_EOL;
$arrayObj->offsetUnset(3);
if ($arrayObj->offsetExists(2)) {
    echo 'Offset Exists' . PHP_EOL;
}
$arrayObj->offsetSet(5, 'f');
echo $arrayObj->offsetGet(4) . PHP_EOL;
for ($iterator = $arrayObj->getIterator();
     $iterator->valid();
     $iterator->next()) {
    echo $iterator->key() . ' => ' . $iterator->current() . PHP_EOL;
}
```

## CachingIterator

没用过。。。

```php
<?php

$array = ['a', 'b', 'c', 'd'];

try {
    $object = new CachingIterator(new ArrayIterator($array));
    foreach ($object as $value) {
        echo $value . PHP_EOL;
    }
} catch (Exception $e) {
    echo $e->getMessage();
}
```

## DirectoryIterator

用来遍历目录，获取文件的信息很方便

```php
<?php

try {
    foreach (new DirectoryIterator(__DIR__) as $item) {
        if ($item->getFilename() == 'ArrayObject.php') {
            echo 'getFilename()->' . $item->getFilename() . PHP_EOL;
            echo 'isDot()->' . $item->isDot() . PHP_EOL;
            echo '__toString()->' . $item->__toString() . PHP_EOL;
            echo 'getPath()->' . $item->getPath() . PHP_EOL;
            echo 'getPathname()->' . $item->getPathname() . PHP_EOL;
            echo 'getPerms()->' . $item->getPerms() . PHP_EOL;
            echo 'getInode()->' . $item->getInode() . PHP_EOL;
            echo 'getSize()->' . $item->getSize() . PHP_EOL;
            echo 'getOwner()->' . $item->getOwner() . PHP_EOL;
            echo 'getGroup()->' . $item->getGroup() . PHP_EOL;
            echo 'getATime()->' . $item->getATime() . PHP_EOL;
            echo 'getMTime()->' . $item->getMTime() . PHP_EOL;
            echo 'getCTime()->' . $item->getCTime() . PHP_EOL;
            echo 'getType()->' . $item->getType() . PHP_EOL;
            echo 'isWritable()->' . $item->isWritable() . PHP_EOL;
            echo 'isReadable()->' . $item->isReadable() . PHP_EOL;
            echo 'isExecutable()->' . $item->isExecutable() . PHP_EOL;
            echo 'isFile()->' . $item->isFile() . PHP_EOL;
            echo 'isDir()->' . $item->isDir() . PHP_EOL;
            echo 'isLink()->' . $item->isLink() . PHP_EOL;
            echo 'getFileInfo()->' . $item->getFileInfo() . PHP_EOL;
            echo 'getPathInfo()->' . $item->getPathInfo() . PHP_EOL;
            echo 'setFileClass()->' . $item->setFileClass() . PHP_EOL;
            echo 'setInfoClass()->' . $item->setInfoClass() . PHP_EOL;
            echo 'openFile()->' . $item->openFile() . PHP_EOL;
        }
    }
} catch (Exception $e) {
    echo 'No files Found!' . PHP_EOL;
}
```

## FilterIterator

过滤指定条件的值，感觉效果和array_filter差不多，没用过

```php
<?php

$animals = ['koala', 'kangaroo', 'wombat', 'wallaby', 'emu', 'NZ' => 'kiwi', 'kookaburra', 'platypus'];

class CullingIterator extends FilterIterator
{

    /*** The filteriterator takes  a iterator as param: ***/
    public function __construct(Iterator $it)
    {
        parent::__construct($it);
    }

    /*** check if key is numeric ***/
    function accept()
    {
        return is_numeric($this->key());
    }
}

/*** end of class ***/
$cull = new CullingIterator(new ArrayIterator($animals));

foreach ($cull as $key => $value) {
    echo $key . '=>' . $value . PHP_EOL;
}
```

## IteratorAggregate

没使用过

```php
<?php
class MyIteratorAggregate implements IteratorAggregate
{
    public $property1 = "Public property one";
    public $property2 = "Public property two";
    public $property3 = "Public property three";

    public $arr = [1, 2, 3, 4, 5, 6];

    public function __construct()
    {
        $this->property4 = "last property";
    }

    /**
     * 聚合迭代器本身不实现迭代的5个方法接口(rewind valid key current next),
     * 委托ArrayIterator实现
     *
     * @return ArrayIterator
     * @author jiangyi
     */
    public function getIterator()
    {
        //return new ArrayIterator($this);
        return new ArrayIterator($this->arr);
    }
}

$obj = new MyIteratorAggregate;

foreach ($obj as $key => $value) {
    var_dump($key, $value);
    echo "\n";
}
```

## LimitIterator

没啥用处

```php
/*** the offset value ***/
$offset = 3;

/*** the limit of records to show ***/
$limit = 2;

$array = ['koala', 'kangaroo', 'wombat', 'wallaby', 'emu', 'kiwi', 'kookaburra', 'platypus'];

$it = new LimitIterator(new ArrayIterator($array), $offset, $limit);

foreach ($it as $k => $v) {
    //echo $it->getPosition() . PHP_EOL;
    echo $k .'=>'.$v . PHP_EOL;
}

$array = ['koala', 'kangaroo', 'wombat', 'wallaby', 'emu', 'kiwi', 'kookaburra', 'platypus'];

$it = new LimitIterator(new ArrayIterator($array));

try {
    $it->seek(5);
    echo $it->current();
} catch (OutOfBoundsException $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

## RecursiveIteratorIterator

递归迭代器

```php
<?php
$array = [
    ['name' => 'butch', 'sex' => 'm', 'breed' => 'boxer'],
    ['name' => 'fido', 'sex' => 'm', 'breed' => 'doberman'],
    ['name' => 'girly', 'sex' => 'f', 'breed' => 'poodle'],
];

foreach (new RecursiveIteratorIterator(new RecursiveArrayIterator($array)) as $key => $value) {
    echo $key . '->' . $value . PHP_EOL;
}
```

## SimpleXMLIterator

可以用来解析XML

```php
<?php

/*** a simple xml tree ***/
$xmlstring = <<<XML
<?xml version = "1.0" encoding="UTF-8" standalone="yes"?>
<document>
  <animal>
    <category id="48">
      <species>monotremes</species>
      <type>platypus</type>
      <name>Bruce</name>
    </category>
  </animal>
  <animal>
    <category id="4">
      <species>arachnid</species>
      <type>funnel web</type>
      <name>Bruce</name>
      <legs>8</legs>
    </category>
  </animal>
</document>
XML;

/*** a new simpleXML iterator object ***/
try {
    /*** a new simple xml iterator ***/
    $it = new SimpleXMLIterator($xmlstring);
    /*** a new limitIterator object ***/
    foreach (new RecursiveIteratorIterator($it, 1) as $name => $data) {
        //echo $name . '=>' . $data . PHP_EOL;
        if ($name == 'animal') {
            //var_dump($data);
        }
    }
} catch (Exception $e) {
    echo $e->getMessage();
}

try {
    /*** a new simpleXML iterator object ***/
    $sxi =  new SimpleXMLIterator($xmlstring);

    /*** set the xpath ***/
    $foo = $sxi->xpath('animal/category/species');

    /*** iterate over the xpath ***/
    foreach ($foo as $k=>$v)
    {
        echo $v.PHP_EOL;
    }
}
catch(Exception $e)
{
    echo $e->getMessage();
}
```
