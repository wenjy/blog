---
url: /2020/05/12/bitmap.html
title: "精确算法bitmap"
date: 2020-05-12T18:31:57+08:00
draft: false
tags: ["Algorithms"]
tags_weight: 100
categories: ["Algorithms"]
categoryes_weight: 100
---

本文参考：[陶加涛 https://www.infoq.cn/article/j3b2Eoz_V7Wzm27odwXX](https://www.infoq.cn/article/j3b2Eoz_V7Wzm27odwXX)

## Bitmap的原理

定义了一个很大的 bit 数组，每个元素对应到 bit 数组的其中一位。

例如有一个集合`[2,3,5,8]`对应的 Bitmap 数组是`[001101001]`，集合中的 2 对应到数组 index 为 2 的位置，3 对应到 index 为 3 的位置，
得到的这样一个数组，我们就称之为 Bitmap。

数组中 1 的数量就是集合的基数。

我们可以把 Bitmap 想象成一个容器，我们知道一个 Integer 是 32 位的，如果一个 Bitmap 可以存放最多 Integer.MAX_VALUE 个值，那么这个 Bitmap 最少需要 32 的长度。
一个 32 位长度的 Bitmap 占用的空间是 512 M （2^32/8/1024/1024）字节。

这种 Bitmap 存在着非常明显的问题：这种 Bitmap 中不论只有 1 个元素或者有 40 亿个元素，它都需要占据 512 M 的空间。

## Roaring Bitmap

Roaring Bitmap 把一个 32 位的 Integer 划分为高 16 位和低 16 位，取高 16 位找到该条数据所对应的 key，每个 key 都有自己的一个 Container。
我们把剩余的低 16 位放入该 Container 中。
依据不同的场景，有 3 种不同的 Container，分别是 Array Container、Bitmap Container 和 Run Container

### Array Container

Array Container 适合存放稀疏的数据，Array Container 内部的数据结构是一个 short array，这个 array 是有序的，方便查找。
数组初始容量为 4，数组最大容量为 4096。超过最大容量 4096 时，会转换为 Bitmap Container。

这边举例来说明数据放入一个 Array Container 的过程：
有 0xFFFF0000 和 0xFFFF0001 两个数需要放到 Bitmap 中, 它们的前 16 位都是 FFFF，所以他们是同一个 key，它们的后 16 位存放在同一个 Container 中 ; 
它们的后 16 位分别是 0 和 1, 在 Array Container 的数组中分别保存 0 和 1 就可以了，
相较于原始的 Bitmap 需要占用 512M 内存来存储这两个数，这种存放实际只占用了 2+4=6 个字节（key 占 2 Bytes，两个 value 占 4 Bytes，不考虑数组的初始容量）。

### Bitmap Container

Bitmap Container，其原理就是上文说的 Bitmap。它的数据结构是一个 long 的数组，数组容量固定为 1024，和上文的 Array Container 不同，Array Container 是一个动态扩容的数组。

这边推导下 1024 这个值：
由于每个 Container 还需处理剩余的后 16 位数据，使用 Bitmap 来存储需要 8192 Bytes（2^16/8）, 而一个 long 值占 8 个 Bytes，所以一共需要 1024（8192/8）个 long 值。
所以一个 Bitmap container 固定占用内存 8 KB（1024 * 8 Byte）。

当 Array Container 中元素到 4096 个时，也恰好占用 8 k（4096*2Bytes）的空间，正好等于 Bitmap 所占用的 8 KB。
而当你存放的元素个数超过 4096 的时候，Array Container 的大小占用还是会线性的增长，但是 Bitmap Container 的内存空间并不会增长，始终还是占用 8 K，
所以当 Array Container 超过最大容量（DEFAULT_MAX_SIZE）会转换为 Bitmap Container。

### Run Container

Run Container，这种 Container 适用于存放连续的数据。
比如说 1 到 100，一共 100 个数，这种类型的数据称为连续的数据。这边的 Run 指的是 Run Length Encoding（RLE），它对连续数据有比较好的压缩效果。
原理是对于连续出现的数字, 只记录初始数字和后续数量。
例如: 对于 `[11, 12, 13, 14, 15, 21, 22]`，会被记录为 `11, 4, 21, 1`。
很显然，该 Container 的存储占用与数据的分布紧密相关。最好情况是如果数据是连续分布的，就算是存放 65536 个元素，也只会占用 2 个 short。
而最坏的情况就是当数据全部不连续的时候，会占用 128 KB 内存（每个元素都多了一个表示长度的数）。

Redis 实现了位图
