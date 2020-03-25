---
url: /2017/12/20/php-spl-datastructures.html
title: "PHP Spl Datastructures"
date: 2017-12-20T14:53:12+08:00
draft: false
tags: ["PHP"]
tags_weight: 100
categories: ["PHP"]
categoryes_weight: 100
---
[PHP文档](https://www.php.net/manual/zh/spl.datastructures.php)

## SplDoublyLinkedList

双向链表

```php
<?php
$dlist = new SplDoublyLinkedList();

// 末端压入
$dlist->push('hiramariam');
$dlist->push('maaz');
$dlist->push('zafar');

// 首端压入
$dlist->unshift(1);
$dlist->unshift(2);
$dlist->unshift(3);

/* the list now contains
3
2
1
hiramariam
maaz
zafar
*/

echo $dlist->pop() . PHP_EOL; // zafar
echo $dlist->shift() . PHP_EOL; // 3

$dlist->add(3, 2.24);

for ($dlist->rewind(); $dlist->valid(); $dlist->next()) {

    echo $dlist->current() . PHP_EOL;
}


$list = new SplDoublyLinkedList();
$list->push('a');
$list->push('b');
$list->push('c');

echo 'FIFO (First In First Out) :' . PHP_EOL;
$list->setIteratorMode(SplDoublyLinkedList::IT_MODE_FIFO);
for ($list->rewind(); $list->valid(); $list->next()) {
    echo $list->current() . PHP_EOL;
}
echo "LIFO (Last In First Out) :" . PHP_EOL;
$list->setIteratorMode(SplDoublyLinkedList::IT_MODE_LIFO);
for ($list->rewind(); $list->valid(); $list->next()) {
    echo $list->current() . PHP_EOL;
}

echo 'test While:' . PHP_EOL;
$splDoubleLinkedList = new SplDoublyLinkedList();
$splDoubleLinkedList->push('a');
$splDoubleLinkedList->push('3');
$splDoubleLinkedList->push('v');
//First of all, we need rewind list
$splDoubleLinkedList->rewind();
//Use while, check if the list has valid node
while ($splDoubleLinkedList->valid()) {
    //Print current node's value
    echo $splDoubleLinkedList->current() . PHP_EOL;
    //Turn the cursor to next node
    $splDoubleLinkedList->next();
}
```

## SplFileObject

解析文件用的，f*函数

```php
<?php
try{
    // iterate directly over the object
    foreach( new SplFileObject(__DIR__.'/spl_file.log') as $line)
    // and echo each line of the file
    echo $line;
}
catch (Exception $e)
{
    echo $e->getMessage();
}

try{
    $file = new SplFileObject(__DIR__.'/spl_file.log');

    $file->seek(3);

    echo $file->current();
}
catch (Exception $e)
{
    echo $e->getMessage();
}
```

## SplFixedArray

一个固定长度的数组

```php
<?php
// Initialize the array with a fixed length
$array = new SplFixedArray(5);

$array[1] = 2;
$array[4] = "foo";

var_dump($array[0]); // NULL
var_dump($array[1]); // int(2)

var_dump($array["4"]); // string(3) "foo"

// Increase the size of the array to 10
$array->setSize(10);

$array[9] = "asdf";
var_dump($array[9]);

// Shrink the array to a size of 2
$array->setSize(2);

// The following lines throw a RuntimeException: Index invalid or out of range
try {
    var_dump($array["non-numeric"]);
} catch (RuntimeException $re) {
    echo "RuntimeException: " . $re->getMessage() . "\n";
}

try {
    var_dump($array[-1]);
} catch (RuntimeException $re) {
    echo "RuntimeException: " . $re->getMessage() . "\n";
}

try {
    var_dump($array[5]);
} catch (RuntimeException $re) {
    echo "RuntimeException: " . $re->getMessage() . "\n";
}
```

## SplHeap

堆的实现

```php
<?php
class JupilerLeague extends SplHeap
{
    /**
     * We modify the abstract method compare so we can sort our
     * rankings using the values of a given array
     */
    public function compare($array1, $array2)
    {
        $values1 = array_values($array1);
        $values2 = array_values($array2);
        if ($values1[0] === $values2[0]) {
            return 0;
        }
        return $values1[0] < $values2[0] ? -1 : 1;
    }
}

$heap = new JupilerLeague();
$heap->insert(['AA Gent' => 15]);
$heap->insert(['Anderlecht' => 20]);
$heap->insert(['Cercle Brugge' => 11]);

// For displaying the ranking we move up to the first node
$heap->top();

// Then we iterate through each node for displaying the result
while ($heap->valid()) {
    foreach ($heap->current() as $team => $score) {
        echo $team . ': ' . $score . PHP_EOL;
    }
    $heap->next();
}
```

## SplMaxHeap

顶部最大堆

```php
class MySimpleHeap extends SplMaxHeap
{

}

$obj = new MySimpleHeap();
$obj->insert(4);
$obj->insert(8);
$obj->insert(1);
$obj->insert(0);

foreach ($obj as $number) {
    echo $number . PHP_EOL;
}
```

## MySimpleHeap

顶部最小堆

```php
<?php
class MySimpleHeap extends SplMinHeap
{

}

$obj = new MySimpleHeap();
$obj->insert(4);
$obj->insert(8);
$obj->insert(1);
$obj->insert(0);

foreach ($obj as $number) {
    echo $number . PHP_EOL;
}
```

## SplStack

栈，通过使用一个双向链表来提供栈的主要功能

```php
$q = new SplStack();

$q[] = 1;
$q[] = 2;
$q[] = 3;
$q->push(4);
$q->add(4, 5);

$q->rewind();
while ($q->valid()) {
    echo $q->current(), "\n";
    $q->next();
}

class Stack
{

    private $splstack;

    function __construct(\SplStack $splstack)
    {
        $this->splstack = $splstack;
    }

    public function calculateSomme()
    {

        if ($this->splstack->count() > 1) {
            $val1 = $this->splstack->pop();
            $val2 = $this->splstack->pop();
            $val = $val1 + $val2;
            $this->splstack->push($val);
            $this->calculateSomme();
        }
    }

    /**
     *
     * @return integer
     */
    public function displaySomme()
    {
        $result = $this->splstack->pop();
        return $result;
    }

}

$splstack = new \SplStack();

$splstack->push(10);
$splstack->push(10);
$splstack->push(10);

$stack = new Stack($splstack);
$stack->calculateSomme();
echo $stack->displaySomme() . PHP_EOL; // 30

$stack = new SplStack();
$stack->push('a');
$stack->push('b');
$stack->push('c');
echo $stack->offsetGet(0);
$stack->offsetSet(0, 'C'); # the last element has index zero

$stack->rewind();

while ($stack->valid()) {
    echo $stack->current(), PHP_EOL;
    $stack->next();
}
```

## SplQueue

队列，通过使用一个双向链表来提供队列的主要功能

```php
<?php
$queue = new SplQueue();
$queue->enqueue('A');
$queue->enqueue('B');
$queue->enqueue('C');

$queue->rewind();
while($queue->valid()){
    echo $queue->current(),"\n";
    $queue->next();
}
echo $queue->dequeue(); //remove first one
```

## SplPriorityQueue

优先队列

```php
<?php
class PQtest extends SplPriorityQueue
{
    public function compare($priority1, $priority2)
    {
        if ($priority1 === $priority2) {
            return 0;
        }
        return $priority1 < $priority2 ? -1 : 1;
    }
}

$objPQ = new PQtest();

$objPQ->insert('A', 3);
$objPQ->insert('B', 6);
$objPQ->insert('C', 1);
$objPQ->insert('D', 2);

echo "COUNT->" . $objPQ->count() . PHP_EOL;

//mode of extraction
$objPQ->setExtractFlags(PQtest::EXTR_BOTH);

//Go to TOP
$objPQ->top();

while ($objPQ->valid()) {
    $value = $objPQ->current();
    echo 'data->' . $value['data'] . '==priority->' . $value['priority'] . PHP_EOL;
    $objPQ->next();
}
```
