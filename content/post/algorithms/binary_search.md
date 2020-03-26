---
url: /2019/03/01/algorithms-binary_search.html
title: "算法-二分查找"
date: 2019-03-01T22:31:57+08:00
draft: false
tags: ["PHP", "Algorithms"]
tags_weight: 100
categories: ["PHP", "Algorithms"]
categoryes_weight: 100
---

## 二分查找简介

```
二分查找又称折半查找，优点是比较次数少，查找速度快，平均性能好；其缺点是要求待查表为有序表，且插入删除困难。
因此，折半查找方法适用于不经常变动而查找频繁的有序列表。
首先，假设表中元素是按升序排列，将表中间位置记录的关键字与查找关键字比较，如果两者相等，则查找成功；
否则利用中间位置记录将表分成前、后两个子表，如果中间位置记录的关键字大于查找关键字，则进一步查找前一子表，否则进一步查找后一子表。
重复以上过程，直到找到满足条件的记录，使查找成功，或直到子表不存在为止，此时查找不成功。

```

## 复杂度

```
因为二分查找每次排除掉一半的不适合值，所以对于n个元素的情况：

一次二分剩下：n/2
两次二分剩下：n/2/2 = n/4
…
m次二分剩下：n/(2^m)

在最坏情况下是在排除到只剩下最后一个值之后得到结果，即

n/(2^m)=1

所以由上式可得 ： 2^m=n

进而可求出时间复杂度为： log2(n)
```

## 代码实现

```php
<?php
/**
 * @param array $data
 * @param $search
 * @return int|null 返回查询到的键名
 */
function binary_search(array $data, $search)
{
    $low = 0;
    $high = count($data) - 1;

    while ($low <= $high) {
        $mid = (int)(($low + $high) / 2);

        $guess = $data[$mid];

        if ($guess == $search) {
            return $mid;
        } elseif ($guess > $search) {
            $high = $mid - 1;
        } else {
            $low = $mid + 1;
        }
    }
    return null;
}

$array = [1, 3, 5, 6, 8, 9, 14, 15];

var_dump(binary_search($array, 0));
```
