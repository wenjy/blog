---
url: /2019/04/01/algorithms-probability.html
title: "算法-简单的概率算法"
keywords: "算法,简单的概率算法"
description: "简单的概率算法"
date: 2019-04-01T22:31:57+08:00
draft: false
tags: ["PHP", "Algorithms"]
tags_weight: 100
categories: ["PHP", "Algorithms"]
categoryes_weight: 100
---

## 经典概率算法简介

```
假设数组为：array(100,200,300,400)，
开始是从1,1000 这个概率范围内筛选第一个数是否在他的出现概率范围之内，
如果不在，则将概率空间，也就是k的值减去刚刚的那个数字的概率空间，
在本例当中就是减去100，也就是说第二个数是在1，900这个范围内筛选的。
这样 筛选到最终，总会有一个数满足要求。
就相当于去一个箱子里摸东西，
第一个不是，第二个不是，第三个还不是，那最后一个一定是。

可用于中奖概率计算，权重等
```
## 代码实现

```php
<?php
class Prize
{
    private $server_info;

    private $weight_arr;

    public function __construct(array $server_info)
    {
        $this->server_info = $server_info;
    }

    private function getWeightArr()
    {
        if (is_null($this->weight_arr)) {
            $this->weight_arr = array_column($this->server_info, 'weight', 'server_mark');
        }
        return $this->weight_arr;
    }

    public function getServerMark() {
        $result = '';
        // 概率数组的总概率精度
        $weight_sum = array_sum($this->getWeightArr());
        // 概率数组循环
        foreach ($this->getWeightArr() as $server_mark => $weight) {
            $rand_num = mt_rand(1, $weight_sum);
            if ($rand_num <= $weight) {
                $result = $server_mark;
                break;
            } else {
                $weight_sum -= $weight;
            }
        }
        return $result;
    }
}

$server_info = [
    'link-rs1' => [
        'server_mark' => 'link-rs1',
        'weight' => 1,
    ],
    'link-rs2' => [
        'server_mark' => 'link-rs2',
        'weight' => 2,
    ],
    'link-rs3' => [
        'server_mark' => 'link-rs3',
        'weight' => 3,
    ],
    'link-rs4' => [
        'server_mark' => 'link-rs4',
        'weight' => 4,
    ]
];

$prize = new Prize($server_info);

$rings = [];
for ($i = 1; $i <= 30000; $i++) {
    $node = $prize->getServerMark();
    $rings[$node] = isset($rings[$node]) ? ++$rings[$node] : 1;
}

//sort($rings);
var_dump($rings);
var_dump(array_sum($rings));
```
