---
url: /2020/04/25/nat.html
title: "NAT原理"
keywords: "NAT原理"
description: "NAT原理"
date: 2020-04-25T22:53:12+08:00
draft: false
tags: ["NAT", "Network"]
tags_weight: 100
categories: ["NAT"]
categoryes_weight: 100
reprintPolicy: noreprint
---

## 简介

NAT英文全称是“Network Address Translation”，中文意思是“网络地址转换”。
允许一个整体机构以一个公用IP（Internet Protocol）地址出现在Internet上。顾名思义，它是一种把内部私有网络地址（IP地址）转换成合法网络IP地址的技术。
因此我们可以认为，NAT在一定程度上，能够有效的解决公网地址不足的问题。（也有人说这是阻碍IPV6发展的原因之一）

## 分类

- 静态NAT(Static NAT)

- 动态地址NAT(Pooled NAT)

- 网络地址端口转换NAPT(Port-Level NAT)

（Network Address Port Translation）是把内部地址映射到外部网络的一个IP地址的不同端口上。它可以将中小型的网络隐藏在一个合法的IP地址后面。
NAPT与 动态地址NAT不同，它将内部连接映射到外部网络中的一个单独的IP地址上，同时在该地址上加上一个由NAT设备选定的端口号。
NAPT是使用最普遍的一种转换方式，在HomeGW中也主要使用该方式。它又包含两种转换方式：SNAT和DNAT。

（Source NAT）：修改数据包的源地址。SNAT改变第一个数据包的来源地址，它永远会在数据包发送到网络之前完成，数据包伪装就是一具SNAT的例子。

（Destination NAT，DNAT）：修改数据包的目的地址。DNAT刚好与SNAT相反，它是改变第一个数据包的目的地地址，如平衡负载、端口转发和透明代理就是属于DNAT。

## 原理

- 地址转换

NAT的基本工作原理是，当私有网主机和公共网主机通信的IP包经过NAT网关时，将IP包中的源IP或目的IP在私有IP和NAT的公共IP之间进行转换。

如下图所示，NAT网关有2个网络端口，其中公共网络端口的IP地址是统一分配的公共 IP，为202.20.65.5；
私有网络端口的IP地址是保留地址，为192.168.1.1。私有网中的主机192.168.1.2向公共网中的主机202.20.65.4发送了1个IP包(Dst=202.20.65.4,Src=192.168.1.2)。

![NAT工作原理1](/images/nat_1.jpg)

当IP包经过NAT网关时，NAT Gateway会将IP包的源IP转换为NAT Gateway的公共IP并转发到公共网，此时IP包（Dst=202.20.65.4，Src=202.20.65.5）中已经不含任何私有网IP的信息。
由于IP包的源IP已经被转换成NAT Gateway的公共IP，Web Server发出的响应IP包（Dst= 202.20.65.5,Src=202.20.65.4）将被发送到NAT Gateway。

这时，NAT Gateway会将IP包的目的IP转换成私有网中主机的IP，然后将IP包（Des=192.168.1.2，Src=202.20.65.4）转发到私有网。
对于通信双方而言，这种地址的转换过程是完全透明的。转换示意图如下：

![转换示意图](/images/nat_2.jpg)

- 连接追踪

在上述过程中，NAT Gateway在收到响应包后，就需要判断将数据包转发给谁。此时如果子网内仅有少量客户机，可以用静态NAT手工指定；
但如果内网有多台客户机，并且各自访问不同网站，这时候就需要连接追踪（connection track）。如下图所示：

![连接追踪](/images/nat_3.jpg)

在NAT Gateway收到客户机发来的请求包后，做源地址转换，并且将该连接记录保存下来，当NAT Gateway收到服务器来的响应包后，查找Track Table，确定转发目标，做目的地址转换，转发给客户机。

- 端口转换

以上述客户机访问服务器为例，当仅有一台客户机访问服务器时，NAT Gateway只须更改数据包的源IP或目的IP即可正常通讯。
但是如果Client A和Client B同时访问Web Server，那么当NAT Gateway收到响应包的时候，就无法判断将数据包转发给哪台客户机，如下图所示：

![端口转换1](/images/nat_4.jpg)

此时，NAT Gateway会在Connection Track中加入端口信息加以区分。
如果两客户机访问同一服务器的源端口不同，那么在Track Table里加入端口信息即可区分，如果源端口正好相同，那么在时行SNAT和DNAT的同时对源端口也要做相应的转换，如下图所示：

![端口转换2](/images/nat_5.jpg)

## 应用

数据伪装: 可以将内网数据包中的地址信息更改成统一的对外地址信息，不让内网主机直接暴露在因特网上，保证内网主机的安全。同时，该功能也常用来实现共享上网。

端口转发: 当内网主机对外提供服务时，由于使用的是内部私有IP地址，外网无法直接访问。因此，需要在网关上进行端口转发，将特定服务的数据包转发给内网主机。

负载均衡: 目的地址转换NAT可以重定向一些服务器的连接到其他随机选定的服务器（被负载均衡的服务器就相当于上面的内网主机）。

失效终结: 目的地址转换NAT可以用来提供高可靠性的服务。如果一个系统有一台通过路由器访问的关键服务器，一旦路由器检测到该服务器宕机（心跳检测），它可以使用目的地址转换NAT透明地把连接转移到一个备份服务器上。

透明代理: NAT可以把连接到因特网的HTTP连接重定向到一个指定的HTTP代理服务器以缓存数据和过滤请求。一些因特网服务提供商就使用这种技术来减少带宽的使用而不用让他们的客户配置他们的浏览器支持代理连接。

转载链接：

[风吹过的时光 https://blog.csdn.net/hzhsan/article/details/45038265](https://blog.csdn.net/hzhsan/article/details/45038265)

[芷菁博客 http://www.stars625.com/nat.html](http://www.stars625.com/nat.html)
