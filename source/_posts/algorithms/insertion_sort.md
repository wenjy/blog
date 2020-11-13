---
url: /2019/03/01/algorithms-insertion_sort.html
title: "算法-插入排序"
keywords: "算法,插入排序"
description: "插入排序"
date: 2019-03-01T22:31:57+08:00
draft: false
tags: ["PHP", "算法"]
tags_weight: 100
categories: ["PHP", "算法"]
categoryes_weight: 100
---

## 插入排序简介

```
插入排序（Insertion sort）是一种简单直观且稳定的排序算法。
如果有一个已经有序的数据序列，要求在这个已经排好的数据序列中插入一个数，但要求插入后此数据序列仍然有序，
这个时候就要用到一种新的排序方法——插入排序法,算法适用于少量数据的排序，时间复杂度O(n^2)。
是稳定的排序方法。插入算法把要排序的数组分成两部分：第一部分包含了这个数组的所有元素，
但将最后一个元素除外（让数组多一个空间才有插入的位置），而第二部分就只包含这一个元素（即待插入元素）。
在第一部分排序完成后，再将这个最后元素插入到已排好序的第一部分中。

插入排序的基本思想是：每步将一个待排序的记录，按其关键码值的大小插入前面已经排序的文件中适当位置上，直到全部插入完为止。
```

## 代码实现

```php
<?php
/**
 * @param array $arr
 * @param string $sort
 * @return array
 */
function insertionSort(array $arr, $sort = 'ASC')
{
    $length = count($arr);
    for ($j = 1; $j < $length; $j++) {
        $key = $arr[$j];
        // 前一位
        $i = $j - 1;
        // 把 $key 依次和前面的数比较
        // 第一次比较，如果前一位数比 $key 大，就把前一位数代替 $key ，这样前一位就空出来了
        // 后面的比较，如果再比 $key 大，则向后移一位（因为前面的数已经是顺序的了）
        // 直到遇到不比 $key 大的，或者比完了
        while ($i >= 0 && (($sort === 'ASC' && $arr[$i] > $key) || ($sort === 'DESC' && $arr[$i] < $key))) {
            $arr[$i + 1] = $arr[$i];
            $i--;
        }
        // 最后把 $key 补上空位
        $arr[$i + 1] = $key;
    }
    return $arr;
}

$arr = [4, 3, 2, 1];
//var_dump(insertionSort($arr, 'DESC'));
var_dump(insertionSort($arr, 'ASC'));
```
