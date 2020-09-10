---
url: /2019/03/01/algorithms-bubble-sort.html
title: "算法-冒泡排序"
keywords: "算法,冒泡排序"
description: "算法冒泡排序"
date: 2019-03-01T22:31:57+08:00
draft: false
tags: ["PHP", "算法"]
tags_weight: 100
categories: ["PHP", "算法"]
categoryes_weight: 100
---

## 冒泡排序简介

```
冒泡排序（Bubble Sort），是一种计算机科学领域的较简单的排序算法。
它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。
走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。
这个算法的名字由来是因为越大的元素会经由交换慢慢“浮”到数列的顶端，故名
```

## 复杂度

```
最坏情况下的时间复杂度都是O(n^2)，最好情况下都是O(n)，空间复杂度是O(1)
```

## 代码实现

```php
<?php
/**
 * @param array $arr
 * @return array
 */
function bubbleSort(array $arr)
{
    $len = count($arr);
    //设置一个空数组 用来接收冒出来的泡
    //该层循环控制 需要冒泡的轮数
    for ($i = 1; $i < $len; $i++) {
        //该层循环用来控制每轮 冒出一个数 需要比较的次数
        for ($k = 0; $k < $len - $i; $k++) {
            if ($arr[$k] > $arr[$k + 1]) {
                $tmp = $arr[$k + 1];
                $arr[$k + 1] = $arr[$k];
                $arr[$k] = $tmp;
            }
        }
    }
    return $arr;
}

$arr = [4, 3, 2, 1];
var_dump(bubbleSort($arr));
```
