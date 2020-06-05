---
url: /2020/06/04/redis-read-error-on-connection.html
title: "一次Redis read error on connection 错误调试"
keywords: "Redis Linux"
description: "Redis read error on connection 错误调试"
date: 2020-06-04T22:53:12+08:00
draft: false
tags: ["Redis", "Linux"]
tags_weight: 100
categories: ["Redis"]
categoryes_weight: 100
---

## 故事之初

一次重启SOA服务后，突然发现有报错`read error on connection to 127.0.0.1:6379`

下面是SOA服务中环境和依赖的包版本

PHP 7.2.25

使用 PCNTL POSIX 来管理进程

[ext-phpredis 5.2.1](https://pecl.php.net/get/redis-5.2.1.tgz)

[ext-event 2.5.4](https://pecl.php.net/get/event-2.5.4.tgz)

laravel 6.18.14

SOA是基于event 写的一个多进程服务 使用laravel command来启动和停止进程

由master 进程 fork 多个work进程处理业务

`ps -ef|grep Soa`

```
root      31614      1  0 Jun04 ?        00:00:00 Soa master server
www       31615  31614  0 Jun04 ?        00:00:01 Soa worker server
www       31616  31614  0 Jun04 ?        00:00:01 Soa worker server
```

## 网上查资料

按照报错，应该是连接被关闭然后再发请求造成的

[参考 Redis：排查 read error on connection 小记](https://www.jianshu.com/p/6d6a09db55e8)

感觉可能是超时时间引起的

于是设置`phpredis` read_timeout 为-1，重启SOA调试，错误依旧

我们redis客户端是启用 persistent 的，使用的 pconnect

看一下redis扩展的配置
```
redis.pconnect.pooling_enabled => 1 => 1
redis.pconnect.connection_limit => 0 => 0
```

配置没什么问题

也去[phpredis](https://github.com/phpredis/phpredis) 源码搜索这个错误，和上面所说的一样

## 重现并调试

既然能稳定重现 那用 `strace -p $pid` 看看系统调用（开始也使用tcpdump gdb 调试，没发现导致的错误的原因）

```
13414 epoll_wait(8, [{EPOLLIN, {u32=11, u64=11}}], 32, -1) = 1
13414 accept(11, {sa_family=AF_INET, sin_port=htons(9342), sin_addr=inet_addr("127.0.0.1")}, [16]) = 12
13414 setsockopt(12, SOL_SOCKET, SO_RCVTIMEO, "2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
13414 setsockopt(12, SOL_SOCKET, SO_SNDTIMEO, "2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
13414 fcntl(12, F_GETFL)                = 0x2 (flags O_RDWR)
13414 fcntl(12, F_SETFL, O_RDWR|O_NONBLOCK) = 0
13414 epoll_ctl(8, EPOLL_CTL_ADD, 12, {EPOLLIN, {u32=12, u64=12}}) = 0
13414 epoll_wait(8, [{EPOLLIN, {u32=12, u64=12}}], 32, -1) = 1
13414 recvfrom(12, "\200", 1, 0, NULL, NULL) = 1
13414 recvfrom(12, "\1\0\1", 3, 0, NULL, NULL) = 3
13414 recvfrom(12, "\0\0\0\16", 4, 0, NULL, NULL) = 4
13414 recvfrom(12, "Device:offLine", 14, 0, NULL, NULL) = 14
13414 recvfrom(12, "\0\0\0\0", 4, 0, NULL, NULL) = 4
13414 recvfrom(12, "\v\0\1\0\0\0 d6e820eebbac8b527d721621a"..., 4096, 0, NULL, NULL) = 40
13414 poll([{fd=7, events=POLLIN|POLLPRI|POLLERR|POLLHUP}], 1, 0) = 0 (Timeout)
13414 sendto(7, "*3\r\n$4\r\nHGET\r\n$33\r\nproxy_server_"..., 93, MSG_DONTWAIT, NULL, 0) = 93
13414 poll([{fd=7, events=POLLIN|POLLPRI|POLLERR|POLLHUP}], 1, 0) = 0 (Timeout)
13414 poll([{fd=7, events=POLLIN|POLLERR|POLLHUP}], 1, 60000) = 1 ([{fd=7, revents=POLLIN}])
13414 recvfrom(7, 0x32e7d40, 8192, MSG_DONTWAIT, NULL, NULL) = -1 EAGAIN (Resource temporarily unavailable)
13414 close(7)                          = 0
13414 write(13, "[2020-06-03 17:36:55] local.ERRO"..., 6179) = 6179
13414 epoll_wait(8, [{EPOLLIN, {u32=11, u64=11}}], 32, -1) = 1
13414 accept(11, {sa_family=AF_INET, sin_port=htons(9348), sin_addr=inet_addr("127.0.0.1")}, [16]) = 7
13414 setsockopt(7, SOL_SOCKET, SO_RCVTIMEO, "2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
13414 setsockopt(7, SOL_SOCKET, SO_SNDTIMEO, "2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
13414 fcntl(7, F_GETFL)                 = 0x2 (flags O_RDWR)
13414 fcntl(7, F_SETFL, O_RDWR|O_NONBLOCK) = 0
13414 epoll_ctl(8, EPOLL_CTL_ADD, 7, {EPOLLIN, {u32=7, u64=7}}) = 0
13414 epoll_wait(8, [{EPOLLIN, {u32=7, u64=7}}], 32, -1) = 1
13414 recvfrom(7, "\200", 1, 0, NULL, NULL) = 1
13414 recvfrom(7, "\1\0\1", 3, 0, NULL, NULL) = 3
13414 recvfrom(7, "\0\0\0\16", 4, 0, NULL, NULL) = 4
13414 recvfrom(7, "Device:offLine", 14, 0, NULL, NULL) = 14
13414 recvfrom(7, "\0\0\0\0", 4, 0, NULL, NULL) = 4
13414 recvfrom(7, "\v\0\1\0\0\0 1e5d42e6117278050b3818af8"..., 4096, 0, NULL, NULL) = 40
13414 socket(AF_INET, SOCK_STREAM, IPPROTO_IP) = 15
13414 fcntl(15, F_GETFL)                = 0x2 (flags O_RDWR)
13414 fcntl(15, F_SETFL, O_RDWR|O_NONBLOCK) = 0
13414 connect(15, {sa_family=AF_INET, sin_port=htons(6379), sin_addr=inet_addr("127.0.0.1")}, 16) = -1 EINPROGRESS (Operation now in progress)
13414 poll([{fd=15, events=POLLIN|POLLOUT|POLLERR|POLLHUP}], 1, 60000) = 1 ([{fd=15, revents=POLLOUT}])
13414 getsockopt(15, SOL_SOCKET, SO_ERROR, [0], [4]) = 0
13414 fcntl(15, F_SETFL, O_RDWR)        = 0
13414 setsockopt(15, SOL_TCP, TCP_NODELAY, [1], 4) = 0
13414 setsockopt(15, SOL_SOCKET, SO_KEEPALIVE, [0], 4) = 0
13414 poll([{fd=15, events=POLLIN|POLLPRI|POLLERR|POLLHUP}], 1, 0) = 0 (Timeout)
13414 sendto(15, "*3\r\n$4\r\nHGET\r\n$33\r\nproxy_server_"..., 93, MSG_DONTWAIT, NULL, 0) = 93
13414 poll([{fd=15, events=POLLIN|POLLPRI|POLLERR|POLLHUP}], 1, 0) = 0 (Timeout)
13414 poll([{fd=15, events=POLLIN|POLLERR|POLLHUP}], 1, 60000) = 1 ([{fd=15, revents=POLLIN}])
13414 recvfrom(15, "-NOAUTH Authentication required."..., 8192, MSG_DONTWAIT, NULL, NULL) = 34
13414 write(13, "[2020-06-03 17:36:55] local.ERRO"..., 6166) = 6166
13414 epoll_wait(8, [{EPOLLIN, {u32=11, u64=11}}], 32, -1) = 1
13414 accept(11, {sa_family=AF_INET, sin_port=htons(9344), sin_addr=inet_addr("127.0.0.1")}, [16]) = 16
13414 setsockopt(16, SOL_SOCKET, SO_RCVTIMEO, "2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
13414 setsockopt(16, SOL_SOCKET, SO_SNDTIMEO, "2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
13414 fcntl(16, F_GETFL)                = 0x2 (flags O_RDWR)
13414 fcntl(16, F_SETFL, O_RDWR|O_NONBLOCK) = 0
13414 epoll_ctl(8, EPOLL_CTL_ADD, 16, {EPOLLIN, {u32=16, u64=16}}) = 0
13414 epoll_wait(8, [{EPOLLIN, {u32=16, u64=16}}], 32, -1) = 1
13414 recvfrom(16, "\200", 1, 0, NULL, NULL) = 1
13414 recvfrom(16, "\1\0\1", 3, 0, NULL, NULL) = 3
13414 recvfrom(16, "\0\0\0\16", 4, 0, NULL, NULL) = 4
13414 recvfrom(16, "Device:offLine", 14, 0, NULL, NULL) = 14
13414 recvfrom(16, "\0\0\0\0", 4, 0, NULL, NULL) = 4
13414 recvfrom(16, "\v\0\1\0\0\0 9eadf923b6dceadd66727a163"..., 4096, 0, NULL, NULL) = 40
13414 poll([{fd=15, events=POLLIN|POLLPRI|POLLERR|POLLHUP}], 1, 0) = 0 (Timeout)
13414 sendto(15, "*3\r\n$4\r\nHGET\r\n$33\r\nproxy_server_"..., 93, MSG_DONTWAIT, NULL, 0) = 93
13414 poll([{fd=15, events=POLLIN|POLLPRI|POLLERR|POLLHUP}], 1, 0) = 0 (Timeout)
13414 poll([{fd=15, events=POLLIN|POLLERR|POLLHUP}], 1, 60000) = 1 ([{fd=15, revents=POLLIN}])
13414 recvfrom(15, "-NOAUTH Authentication required."..., 8192, MSG_DONTWAIT, NULL, NULL) = 34
13414 write(13, "[2020-06-03 17:36:55] local.ERRO"..., 6166) = 6166
13414 epoll_wait(8, [{EPOLLIN, {u32=16, u64=16}}], 32, -1) = 1
13414 recvfrom(16, "", 1, 0, NULL, NULL) = 0
13414 epoll_ctl(8, EPOLL_CTL_DEL, 16, 0x7ffe0c36b200) = 0
13414 shutdown(16, SHUT_RDWR)           = 0
13414 close(16)                         = 0
13414 epoll_wait(8, [{EPOLLIN, {u32=12, u64=12}}], 32, -1) = 1
13414 recvfrom(12, "", 1, 0, NULL, NULL) = 0
13414 epoll_ctl(8, EPOLL_CTL_DEL, 12, 0x7ffe0c36b200) = 0
13414 shutdown(12, SHUT_RDWR)           = 0
13414 close(12)                         = 0
13414 epoll_wait(8, [{EPOLLIN, {u32=7, u64=7}}], 32, -1) = 1
13414 recvfrom(7, "", 1, 0, NULL, NULL) = 0
13414 epoll_ctl(8, EPOLL_CTL_DEL, 7, 0x7ffe0c36b200) = 0
13414 shutdown(7, SHUT_RDWR)            = 0
13414 close(7)                          = 0
13414 epoll_wait(8,  <detached ...>
```

上面的调试 看到了 `EAGAIN (Resource temporarily unavailable)` 然后关闭了连接

下一步redis重连了，重连之后没有发送auth，还报了未验证的错误

去官方看看，重连未验证这个问题已经在`phpredis` 5.2.2解决，于是升级到了 5.2.2 未验证的问题修复了 

看看具体的redis连接情况 `lsof -i:6379`
```
php     17778 root    6u  IPv4 5742941      0t0  TCP 127.0.0.1:7915->127.0.0.1:6379 (ESTABLISHED)
php     17778 root    7u  IPv4 5742440      0t0  TCP 127.0.0.1:7917->127.0.0.1:6379 (ESTABLISHED)
php     17779  www    6u  IPv4 5742941      0t0  TCP 127.0.0.1:7915->127.0.0.1:6379 (ESTABLISHED)
php     17779  www    7u  IPv4 5742440      0t0  TCP 127.0.0.1:7917->127.0.0.1:6379 (ESTABLISHED)
php     17780  www    6u  IPv4 5742941      0t0  TCP 127.0.0.1:7915->127.0.0.1:6379 (ESTABLISHED)
php     17780  www    7u  IPv4 5742440      0t0  TCP 127.0.0.1:7917->127.0.0.1:6379 (ESTABLISHED)
```

每次重启都发现有两个redis连接存在，而且是master进程创建的redis连接

对比php-fpm的redis连接

```
php     57305  www    6u  IPv4 5487078      0t0  TCP 127.0.0.1:31788->127.0.0.1:6379 (ESTABLISHED)
php     57305  www    7u  IPv4 5488024      0t0  TCP 127.0.0.1:31790->127.0.0.1:6379 (ESTABLISHED)
php     57305  www    8u  IPv4 5486093      0t0  TCP 127.0.0.1:31792->127.0.0.1:6379 (ESTABLISHED)
php     57305  www    9u  IPv4 5486094      0t0  TCP 127.0.0.1:31794->127.0.0.1:6379 (ESTABLISHED)
php     57313  www    6u  IPv4 5488037      0t0  TCP 127.0.0.1:31798->127.0.0.1:6379 (ESTABLISHED)
php     57313  www    7u  IPv4 5488038      0t0  TCP 127.0.0.1:31800->127.0.0.1:6379 (ESTABLISHED)
php     57313  www    8u  IPv4 5488043      0t0  TCP 127.0.0.1:31802->127.0.0.1:6379 (ESTABLISHED)
php     57313  www    9u  IPv4 5486102      0t0  TCP 127.0.0.1:31804->127.0.0.1:6379 (ESTABLISHED)
```

仔细看上面的SOA redis连接，发现work进程共享了master的资源，两个work进程有一样的redis连接

继续测试果然发现 redis读取的数据是错乱的，每次都是读取到了上一次的数据

一开始以为laravel加载服务提供者的时候就已经创建了redis连接

于是 在master进程中调用关闭redis连接，然后再work进程中重新注册redis服务提供者，redis连接依然存在

因为线上环境也有一台服务器有这样的问题

于是回到线上环境，线上两台服务器，有一台正常，有一台的问题和我本地环境一样，对比两台服务器的history命令 看看是否和服务启动有关，还是改了什么

通过对比发现正常的那台服务器 是先restart queue 再 restart soa

然后在有问题的那台服务器上重新操作一遍，发现问题居然解决了！是queue影响了吗？queue也是使用laravel command来管理

回到本地测试环境

一样操作，发现问题居然还在，这就有点神奇了 玄学吗（代码的世界是没有玄学的）

最后想到到底在哪里创建了redis连接呢？线上已经验证启动SOA时是没有redis连接的，创建redis连接在具体的业务调用里面

到这里就问题就变成了找到 master 启动时在哪里调用了Redis:connect

最快的方法是看看调用栈，到 laravel 源码 PhpRedisConnector 出直接抛个Exception 看看是哪里调用了 Redis::connect

最后发现是 app/Console/Commands/ProxyUserUsedMigrate 这个文件

然后看看代码，原来我在这类的_ _construct 调用了 redis:connect

而且Commands里面的类 是laravel初始化的时候就全部加载的，这就导致了启动SOA时master已经连接redis了

问题已经明确：

并发测试时，`EAGAIN (Resource temporarily unavailable)` 导致连接被断开，其它子进程还在使用此连接

最终导致了`read error on connection to 127.0.0.1:6379`

至此找到了问题的所在（上面laravel注册服务提供者时，是没有创建redis连接的）

删除这个命令文件 重启soa服务，一切正常了

## 总结

这次的bug是新增代码影响的，而且新增的代码只是临时执行需要，开始没有考虑到这新增的代码会影响到SOA

laravel Commands __construct 里面不要做redis操作 或者其他的连接操作 这样会影响soa master

像这样的问题，还是得先仔细看看最近修改、增加了那些代码，这样比较容易发现问题的所在

从这次调试中也加深了对phpredis的熟悉
