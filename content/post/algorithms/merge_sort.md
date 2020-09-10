---
url: /2019/03/01/algorithms-merge-sort.html
title: "算法-归并排序"
keywords: "算法,归并排序"
description: "归并排序"
date: 2019-03-01T22:31:57+08:00
draft: false
tags: ["PHP", "算法"]
tags_weight: 100
categories: ["PHP", "算法"]
categoryes_weight: 100
---

## 归并排序(快速排序)

```
分治法：将原问题分解为几个规模较小但类似的子问题，递归地求解这些子问题，然后再合并这些子问题的解来建立原问题的解
在最坏、最佳、平均情况下归并排序时间复杂度均为o(nlogn)
```

## 代码实现

```php
<?php
/**
 * @param $arr
 * @param $p
 * @param $q
 * @param $r
 */
function merge(&$arr, $p, $q, $r)
{
    $temp = [];
    // 两个起始点位置
    $i = $p;
    $j = $q + 1;
    // 两个起始点都不能超过各自的终点，这里可能导致其中一个起始点没循环完
    while ($i <= $q && $j <= $r) {
        if ($arr[$i] < $arr[$j]) {
            $temp[] = $arr[$i++];
        } else {
            $temp[] = $arr[$j++];
        }
    }

    // 这里把 $p - $q 未循环完的加入
    while ($i <= $q) {
        $temp[] = $arr[$i++];
    }

    // 这里把 $q + 1 - $r 未循环完的加入
    while ($j <= $r) {
        $temp[] = $arr[$j++];
    }

    // 将排好序的插入原数组
    for ($k = 0, $len = count($temp); $k < $len; $k++) {
        $arr[$p + $k] = $temp[$k];
    }
}

/**
 * @param $arr
 * @param $p
 * @param $r
 */
function mergeSort(&$arr, $p, $r)
{
    //当子序列长度为1时，不用再分组
    if ($p < $r) {
        $q = floor(($p + $r) / 2); // $arr $arr[$p - $q] $arr[$q+1 - $r]
        mergeSort($arr, $p, $q); // $arr[$p - $q]
        mergeSort($arr, $q + 1, $r); // $arr[$q+1 - $r]
        echo 'p->'.$p.' q->'.$q.' r->'.$r.PHP_EOL;
        merge($arr, $p, $q, $r);
    }
}

$arr = [4, 3, 2, 1];
mergeSort($arr, 0, count($arr) - 1);
var_dump($arr);
```
