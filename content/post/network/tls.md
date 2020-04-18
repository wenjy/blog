---
url: /2020/04/14/tls.html
title: "TLS握手通信"
keywords: "TLS握手通信"
description: "TLS握手通信"
date: 2020-04-14T22:53:12+08:00
draft: true
tags: ["TLS"]
tags_weight: 100
categories: ["TLS"]
categoryes_weight: 100
---

## RSA

[阮一峰 RSA算法原理（一）](http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)

[阮一峰 RSA算法原理（二）](http://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html)

- 质数

质数又称素数。一个大于1的自然数，除了1和它自身外，不能被其他自然数整除的数叫做质数

[百度百科 质数](https://baike.baidu.com/item/%E8%B4%A8%E6%95%B0)

- 互质关系

如果两个正整数，除了1以外，没有其他公因子，我们就称这两个数是互质关系（coprime）

互质关系的结论：
```
1. 任意两个质数构成互质关系，比如13和61。

2. 一个数是质数，另一个数只要不是前者的倍数，两者就构成互质关系，比如3和10。

3. 如果两个数之中，较大的那个数是质数，则两者构成互质关系，比如97和57。

4. 1和任意一个自然数是都是互质关系，比如1和99。

5. p是大于1的整数，则p和p-1构成互质关系，比如57和56。

6. p是大于1的奇数，则p和p-2构成互质关系，比如17和15。
```

- 欧拉函数

任意给定正整数n，请问在小于等于n的正整数之中，有多少个与n构成互质关系？

计算这个值的方法就叫做欧拉函数，以φ(n)表示。

分以下情况讨论

```
1. 如果n=1，则 φ(1) = 1 。因为1与任何数（包括自身）都构成互质关系。

2. 如果n是质数，则 φ(n)=n-1 。因为质数与小于它的每一个数，都构成互质关系。比如5与1、2、3、4都构成互质关系。

3. 如果n是质数的某一个次方，即 n = p^k (p为质数，k为大于等于1的整数)，则 φ(p^k) = p^k - p^(k-1) = p^k(1-1/p)。
例如：φ(8) = φ(2^3) = 2^3 - 2^2 = 8 - 4 = 4。

4. 如果n可以分解成两个互质的整数之积，n = p1 * p2，则 φ(n) = φ(p1p2) = φ(p1)φ(p2)

5. 任意一个大于1的正整数，都可以写成一系列质数的积。φ(n) = n(1-1/p1)(1-1/p2)...(1-1/pr)
```

- 欧拉定理

如果两个正整数a和n互质，则n的欧拉函数 φ(n) 可以让下面的等式成立：a^

## TLS1.2

1. 客户端发送 ClientHello, 包含一个随机数 ClientHello.random。

2. 服务器回复 ServerHello，包含一个随机数 ServerHello.random，同时回复 certificate，携带了证书公钥P。

3. 客户端收到 ServerHello.random 之后，就能够生成 pre_master_secret 以及 master_secret。

  其中，pre_master_secret 长度为48个字节，前两个字节是协议版本号，剩下的46个字节填充一个随机数。结构如下：
  `Struct {byte Version[2]; bute random[46];}`
  
  master_secret 的生成算法简述如下：
  `master_secret = PRF(pre_master_secret, "master secret", ClientHello.random+ServerHello.random)`
  
  其中，PRF是一个随机函数，定义如下:
  `PRF(secret, label, seed) = P_MD5(S1, label + seed) XOR P_SHA-1(S2, label + seed)`
  
   而 master_secret 包含了6部分内容，分别是用于校验内容一致性的密钥，用于对称内容加解密的密钥，
   以及初始化向量(用于CBC模式)，客户端和服务器各一份，从上式可以看出，把 pre_master_secret 赋值给 secret， 
   master_key 赋值给 label，客户端和服务器的两个随机数做种子就能确定地求出一个 48位长的随机数。

4. 客户端使用证书公钥P将 pre_master_secret 加密后发送给服务器。

5. 服务器使用私钥解密得到 pre_master_secret，又由于服务端之前就接收了 ClientHello.random ，所以服务器根据相同的生成算法，在相同的输入参数下，求出了相同了 master_secret。

6. 客户端和服务器使用 master_secret 加密数据通信

密钥协商过程需要 2 个 RT(Route Trip)，这也是 HTTPS 慢的一个重要原因。
