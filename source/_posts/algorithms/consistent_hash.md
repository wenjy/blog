---
url: /2019/04/01/algorithms-consistent-hash.html
title: "算法-一致性Hash"
keywords: "算法,一致性Hash"
description: "一致性Hash"
date: 2019-04-01T22:31:57+08:00
draft: false
tags: ["PHP", "算法"]
tags_weight: 100
categories: ["PHP", "算法"]
categoryes_weight: 100
---

## 简介


一致性哈希算法（Consistent Hashing）最早在论文
[《Consistent Hashing and Random Trees: Distributed Caching Protocols for Relieving Hot Spots on the World Wide Web》](https://dl.acm.org/doi/10.1145/258533.258660)
中被提出。简单来说，一致性哈希将整个哈希值空间组织成一个虚拟的圆环，如假设某哈希函数H的值空间为0-2^32-1（即哈希值是一个32位无符号整形）


## PHP实现

```php
class ConsistentHash
{
    /**
     * 圆环 hash => node
     * @var array
     */
    protected $ring = [];

    /**
     * 节点 node => hash
     * @var array
     */
    protected $nodes = [];

    /**
     * 每个节点的虚拟节点，当节点较少时使用虚拟节点使分布更均匀
     * @var int
     */
    protected $virtual = 40;

    /**
     * 标识圆环是否排序过
     * @var bool
     */
    protected $ring_sorted = false;

    public function __construct(array $nodes = [])
    {
        foreach ($nodes as $node) {
            $this->addNode($node);
        }
    }

    /**
     * 添加一个节点
     * @param string $node
     * @return bool
     */
    public function addNode(string $node): bool
    {
        if (key_exists($node, $this->nodes)) {
            return true;
        }
        $this->ring_sorted = false;

        for ($i = 1; $i <= $this->virtual; $i++) {
            $key = $this->time33($node . '-' . $i);
            $this->ring[$key] = $node;
            $this->nodes[$node][] = $key;
        }

        return true;
    }

    /**
     * 获取字符串的HASH在圆环上面映射到的节点
     * @param string $key
     * @return string $node
     */
    public function getNode(string $key): string
    {
        if (!$this->ring_sorted) {
            ksort($this->ring, SORT_NUMERIC);
            $this->ring_sorted = true;
        }

        $node = current($this->ring);
        $hash = $this->time33($key);
        foreach ($this->ring as $k => $v) {
            if ($hash <= $k) {
                $node = $v;
                break;
            }
        }
        return $node;
    }


    /**
     * hash(i) = hash(i-1) * 33 + str[i]
     * @param string $str
     * @return int
     */
    protected function time33(string $str): int
    {
        $str = md5($str);
        $hash = 5381;
//        $hash = 0;
        $seed = 5;
        $len = 32; // 加密后长度32
        for ($i = 0; $i < $len; $i++) {
            // (hash << 5) + hash 相当于 hash * 33
            //$hash = sprintf("%u", $hash * 33) + ord($s{$i});
            //$hash = ($hash * 33 + ord($s{$i})) & 0x7FFFFFFF;
            $hash = ($hash << $seed) + $hash + ord($str{$i});
        }
        return $hash & 0x7FFFFFFF;
    }
}

$ch_obj = new ConsistentHash();
$ch_obj->addNode(random());
$ch_obj->addNode(random());
$ch_obj->addNode(random());
$ch_obj->addNode(random());

$rings = [];
for ($i = 1; $i <= 10000; $i++) {
    $key = md5(random());
    $node = $ch_obj->getNode($key);
    $rings[$node] = isset($rings[$node]) ? ++$rings[$node] : 1;
}

//sort($rings);
var_dump($rings);

function random($length = 16)
{
    $string = '';

    while (($len = strlen($string)) < $length) {
        $size = $length - $len;

        $bytes = random_bytes($size);

        $string .= substr(str_replace(['/', '+', '='], '', base64_encode($bytes)), 0, $size);
    }

    return $string;
}
```

[参考链接](https://juejin.im/post/5ae1476ef265da0b8d419ef2)
