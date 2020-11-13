---
url: /2020/08/18/binary-tree.html
title: "二叉搜索树"
keywords: "二叉树"
description: "二叉树"
date: 2020-08-18T20:31:57+08:00
draft: false
tags: ["算法"]
tags_weight: 100
categories: ["算法"]
categoryes_weight: 100
---

## 二叉树简介

二叉树由节点（node）和边组成。节点分为根节点、父节点、子节点

二叉树的父节点最多有两个子节点，二叉树一个节点左子节点关键字`<`这个父节点，右子节点关键字`>=`这个父节点

## 代码示例

```php
/**
 * 表示节点
 */
class Node
{
    /**
     * @var int 表示索引
     */
    private $index;
    /**
     * @var Node 左节点
     */
    private $leftNode;
    /**
     * @var Node 又节点
     */
    private $rightNode;

    /**
     * Node constructor.
     * @param int $index
     */
    public function __construct(int $index)
    {
        $this->index = $index;
    }

    /**
     * @return int
     */
    public function getIndex(): int
    {
        return $this->index;
    }

    /**
     * @return Node
     */
    public function getLeftNode(): ?Node
    {
        return $this->leftNode;
    }

    /**
     * @return Node
     */
    public function getRightNode(): ?Node
    {
        return $this->rightNode;
    }

    /**
     * @param Node $leftNode
     */
    public function setLeftNode(Node $leftNode): void
    {
        $this->leftNode = $leftNode;
    }

    /**
     * @param Node $rightNode
     */
    public function setRightNode(Node $rightNode): void
    {
        $this->rightNode = $rightNode;
    }
}

/**
 * 表示树
 */
class Tree
{
    /**
     * @var Node 树的根节点
     */
    private $root;

    /**
     * 插入一个索引节点
     * @param int $index
     */
    public function insert(int $index)
    {
        $node = new Node($index);
        if (is_null($this->root)) {
            $this->root = $node;
        } else {
            $current = $this->root;
            $parent = null;
            while (true) {
                $parent = $current; // 保存当current变为null之前的那一个父节点
                // 插入左节点
                if ($index < $current->getIndex()) {
                    $current = $current->getLeftNode();
                    if (is_null($current)) {
                        $parent->setLeftNode($node);
                        return;
                    }
                } else {
                    $current = $current->getRightNode();
                    if (is_null($current)) {
                        $parent->setRightNode($node);
                        return;
                    }
                }
            }
        }
    }

    /**
     * 查找索引节点
     * @param int $index
     * @return Node|null
     */
    public function find(int $index): ?Node
    {
        $current = $this->root;
        if (is_null($current)) {
            return null;
        }

        while ($current->getIndex() != $index) {
            if ($current->getIndex() > $index) {
                $current = $current->getLeftNode();
            } else {
                $current = $current->getRightNode();
            }
            if (is_null($current)) {
                return null;
            }
        }
        return $current;
    }
}

$tree = new Tree();
$tree->insert(3);
$tree->insert(2);
$tree->insert(6);
$tree->insert(4);
$tree->insert(5);
$tree->insert(1);
var_dump($tree->find(1));
```
