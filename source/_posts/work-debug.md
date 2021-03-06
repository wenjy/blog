---
url: /2019/01/01/work-debug.html
title: "工作中遇到的问题+踩过的坑"
date: 2019-10-11T22:31:57+08:00
draft: false
tags: ["PHP"]
tags_weight: 100
categories: ["PHP"]
categoryes_weight: 100
---

##  安装的虚拟机时间不同步

```
ntpdate cn.pool.ntp.org
```


## phpxdebug 运行失败，端口繁忙

```
因为 phpxdebug 端口和 php 默认监听的端口冲突，都是默认9000，把 phpxdebug port 设置为其它端口，如9001
```

## 本地测试环境curl，https时因为没有设置公钥而失败
[设置公钥](https://stackoverflow.com/questions/28858351/php-ssl-certificate-error-unable-to-get-local-issuer-certificate)


## PHP函数long2ip溢出

```
phpstudy 的PHP默认是32位的，long2ip() 时会有问题，需要换装64位的
reids 也是32位，溢出问题
直接替换 phpstudy 的 php 为 64位版本的，但是 redis 在win上没有，最后使用虚拟机了
```



## 升级laravel框架

```
升级框架，laravel/lumen 5.6.* -> 5.8.* 按照5.8.*修改composer.json依赖一致，然后利用gitbug的文件对比功能，修改改变的文件。
使用 github 版本文件对比，修改更改的文件和受影响的改动
```
[Comparing changes](https://github.com/laravel/lumen/compare/5.6...master)


## reids重启后rdb文件没有加载

- 原因 dir 设置的是 ./ 所以启动时没有到 rdb文件的目录导致没有加载，dir应设置为绝对目录 /usr/local/redis/

## 服务经常报502
- ip服务经常报502，但fpm进程并没跑满，后面查看nginx的错误日志发现有connect() to unix:/tmp/php7-cgi.sock failed (11: Resource temporarily unavailable) while connecting to upstream错误

[backlog](https://www.cnxct.com/something-about-phpfpm-s-backlog/)

## git换行符问题

- github clone 下一个项目，结果文件全是改动 eol unix expect windows

git自动转换 CRLF
[CRLF 问题](https://github.com/cssmagic/blog/issues/22)

- locate 在centos软件包叫 mlocate 首次安装需要 updatedb

## 检测ip的延迟

- 使用exce() 来ping 不安全，使用socket_create 构建ICMP ping 也需要特殊权限 suid

[不需要 root 权限的 ICMP ping](https://blog.lilydjwg.me/2013/10/29/non-privileged-icmp-ping.41390.html)

使用SOCK_DGRAM 
`sudo sysctl -w net.ipv4.ping_group_range='0 10'` 设置用户组
[不需要 root](https://www.jianshu.com/p/d77ede976bb3)

最后还是使用 exce() 来ping，先验证IP合法性

## PHP yar扩展报错

- 本机yar 扩展打开了msgpack，服务器端没有支持

[MSGPACK](https://github.com/laruence/yar/issues/113)

- win上phpstudy php使用cgi，并且只有一个进程，代码里yar是循环调用

[WSARecv() failed (10054: An existing connection was forcibly closed by the remote host)](https://blessing.studio/phpstudy-prober-page-502-bad-gateway/)

## laravel http status 419

`laravel 419 Sorry, your session has expired. Please refresh and try again.`

问题是没有加 csrf

[419](https://stackoverflow.com/questions/52583886/post-request-in-laravel-5-7-laravel-5-8-error-419-sorry-your-session-has/53781657#53781657)

## redis 内存出了问题(暂计)

[reids启动警告1](https://blog.csdn.net/jiangshouzhuang/article/details/50864933)

[reids启动警告2](https://www.jianshu.com/p/7ca4b74c92be)

## laravel 迁移报错

mysql5.6 字符集 utf8_mb4 varchar(255)
767/4=191 意思只能 varchar(191)
```
SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long; 
max key length is 767 bytes (SQL: alter table `jobs` add index `jobs_queue_index`(`queue`))
```
[error 1071](https://stackoverflow.com/questions/1814532/1071-specified-key-was-too-long-max-key-length-is-767-bytes)

## cookie无法删除

- 使用 jquery.cookie removeCookie 无法删除cookie的问题

[需要指定path](https://stackoverflow.com/questions/18486165/remove-cookie-using-jquery-not-working)

## gerrit 提交远程被拒绝

原因是 `git config --global user.email "your_email@mail.com"`
我把全局的email修改了，phpstorm读取不到，commit 的时候自己填的email没有用，得选择phpstorm显示的才行

最后我修改回了公司的email，然后commit选择就能push了

## phpunit 异常断言

- phpunit @expectedExceptionMessage 比较时使用的是 strpos

## composer suggest

- Psr\Http\Message\ResponseInterface 编辑器找不到

看了一下安装laravel时貌似没有安装 composer.json 里的 suggest 包，安装时并没有指定 --no-suggest: 跳过扩展包建议，看来是默认不安装的

## laravel-s redis

- laravel-s Redis::connection() 返回redis单例连接，每个 worker 进程只能保持一个连接，只有在max_request达到时
worker 将自动退出，进程退出后会释放所有内存和资源。

## swoole yar 不兼容

## laravel 表单验证失败

不是ajax请求会302，然后 redirect / 就会 404
1. 重写 App\Exceptions\Handler::render 同一验证失败json输出
2. 也可以重写request header 因为是非ajax才会302

## laravel PhpRedisConnection hscan bug

```php
    $it = null;
    $redis->setOption(\Redis::OPT_SCAN, \Redis::SCAN_RETRY);
    while($device_fds = $redis->client()->hScan(Link::getFdMapKey(), $it, '', 1000)) {
        foreach($device_fds as $device_unique_mark => $fd) {
             echo $device_unique_mark . PHP_EOL;
        }
    }
```
laravel框架封装的问题，需要调用$redis->client(),而不是直接使用phpredis，$it 需要引用，PhpRedisConnection 使用 __call 调用存在bug

## 数组参数签名前，最好使用 ksort 排序

##laravel queue redis 

因为设置了 --sleep=3 并且没有配置队列 block_for 导致队列延时执行
删除了 --sleep=3 并且配置 block_for => 3
block_for：将任务重新放入 Redis 数据库以及处理器轮询之前阻塞多久

## php Trait 

定义了一个属性后，类就不能定义同样名称的属性，否则会产生 fatal error。 有种情况例外：属性是兼容的（同样的访问可见度、初始默认值）
可以先定义一个父类使用 Trait，然后子类就能覆写父类的属性了

## PHP可以为一个类提供一个静态方法，实例化自己，延迟绑定
```php
 public static function make($items = [])
 {
     return new static($items);
 }
```
## PHP Mockery::mock
```php
// 需先赋值
$request = Mockery::mock(CurlRequest::class);
// 这里不能连续调用，因为后面返回的不是 $request
$request->shouldReceive('post')
        ->once()
        ->andReturn([
            'status' => 1,
            'msg'    => '请求成功'
        ]);
```

## 2019.7.11 小数精度问题
```php
echo 16.33 * 100; // 1633
echo intval(16.33 * 100); // 1632
echo intval(strval(16.33 * 100)); // 1633
echo (int)(16.33 * 100); // 1632
```

## laravel config:cache 的问题

如果在部署过程中执行 config:cache 命令，那你应该确保只从配置文件内部调用 env 函数。一旦配置被缓存，.env 文件将不再被加载，所有对 env 函数的调用都将返回 null

### php mb_strlen

```php
echo mb_strlen('测试'); // 2 默认编码UTF-8
mb_strlen('测试', 'UTF-8'); // 2
mb_strlen('测试', '8bit'); // 6
strlen('测试'); // 6
// 8bit是php独有解析的, 8bit并不是一个字符集, 只是php引擎可以解析它而已. 顾名思义, 一个字节等于八个位, 1byte=8bit
```

PHP支持mbstring.func_overload, 用于解决原生substr无法有效应对多字节编码字符串问题
启用时php runtime状态是未知的， 所以在计算字符长度时， 用mb_strlen 8bit来保证计算字符串长度的正确性（按照1byte = 8bit）


##PHP 一个if判断变量的速度问题，类型转换

```php
$array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

$start = microtime(true);
for ($i = 0; $i < 1000000; $i++) {
//    if (count($array)) {} // 0.01805305480957
//    if (is_null($array)) {} // 0.013534784317017
//    if (empty($array)) {} // 0.014011859893799
    if (!$array) {} // 0.016890048980713
}
$end = microtime(true);
echo "1: " . ($end - $start) . "\n";
echo memory_get_usage() . "\n"; // 389032
```

## laravel phpredis subscribe 因为使用了redis前缀 channel 返回时需要去掉前缀

## php多进程编程，pcntl posix event，发现grpc扩展会导致进程无法 kill -15

## php apcu扩展需要开启 apc.enable_cli 才能使用apcu_fetch之类的函数

## php composer --ignore-platform-reqs 

可忽略平台约束 例如win下升级 而composer.json require 一些win没有的扩展

## http https 代理解析协议端口时 需要解析 CONNECT HOST 的端口 

## php apcu ttl 在cli下不过期的问题，需要设置apc.use_request_time=0

```php
// cli 下运行
$bar = date('Y-m-d H:i:s');
var_dump(apcu_store('foo', $bar, 3));
sleep(4);
var_dump(apcu_fetch('foo'));
// apc.use_request_time=1 不过期
// 使用请求时间来淘汰缓存
```

## reids scan 命令在数据量比较少时例如count指定的返回偏差较大，大数据集时count相差不大

可使用yield遍历整个数据集

```php
$key = 'test_set_111222';
$items = [];
for ($i = 0; $i < 1000; $i++) {
    $items[] = $i;
}
$this->redis->sAdd($key, ...$items);
foreach ($this->get($key, 20) as $item) {
    var_dump($item);
}
function get($key, int $count) : Generator
{
    $this->redis->setOption(\Redis::OPT_SCAN, \Redis::SCAN_RETRY);
    $it = null;
    while ($item = $this->redis->client()->sScan($key, $it, '*', $count)) {
        yield $item;
    }
}
```

## 下载文件可以使用 header X-Accel-Redirect 来实现nginx层下载，控制速度，减少IO

## Wireshark卡死的原因居然是 有道词典的取词功能造成的

[wireshark为什么会卡死？](https://segmentfault.com/a/1190000019913529)

## socket_set_option 参数顺序

SO_REUSEPORT 必须在 bind() 函数之前设置


## linux TCP连接失败(不回复SYN,ACK)问题

[参考分析问题1](https://blog.csdn.net/ywq935/article/details/91384854)

[net.ipv4.tcp_tw_recycle参数](https://www.cnblogs.com/cheyunhua/p/9082674.html)

## php socket EVENT扩展 死循环的问题

代码中出现未捕获异常，导致死循环

go 模拟RST
```go
package main

import (
	"flag"
	"fmt"
	"net"
	"os"
)

var host = flag.String("host", "172.16.1.49", "host")
var port = flag.String("port", "9999", "port")

func main()  {
	conn, err := net.Dial("tcp", *host+":"+*port)
	fmt.Println(*host+":"+*port)
	if err != nil {
		fmt.Println("err1")
		os.Exit(1)
	}

	defer conn.Close()

	tcpConn, ok := conn.(*net.TCPConn)
	if !ok {
		fmt.Println("err2")
		os.Exit(1)
	}
	tcpConn.SetLinger(0)
}
```

strace 调试日志
```
39070 epoll_wait(6, [{EPOLLIN, {u32=9, u64=9}}], 32, -1) = 1
39070 accept(9, {sa_family=AF_INET, sin_port=htons(35959), sin_addr=inet_addr("127.0.0.1")}, [16]) = 10
39070 setsockopt(10, SOL_SOCKET, SO_RCVTIMEO, "\36\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
39070 setsockopt(10, SOL_SOCKET, SO_SNDTIMEO, "\36\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
39070 fcntl(10, F_GETFL)                = 0x2 (flags O_RDWR)
39070 fcntl(10, F_SETFL, O_RDWR|O_NONBLOCK) = 0
39070 getpeername(10, 0x7fff585ad500, 0x7fff585ad4b4) = -1 ENOTCONN (Transport endpoint is not connected)
39070 epoll_wait(6, [{EPOLLIN, {u32=9, u64=9}}], 32, -1) = 1
39070 epoll_wait(6, [{EPOLLIN, {u32=9, u64=9}}], 32, -1) = 1
39070 epoll_wait(6, [{EPOLLIN, {u32=9, u64=9}}], 32, -1) = 1
...死循环
```

正常的
```
39579 epoll_wait(6, [{EPOLLIN, {u32=9, u64=9}}], 32, -1) = 1
39579 accept(9, {sa_family=AF_INET, sin_port=htons(26949), sin_addr=inet_addr("127.0.0.1")}, [16]) = 10
39579 setsockopt(10, SOL_SOCKET, SO_RCVTIMEO, "\36\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
39579 setsockopt(10, SOL_SOCKET, SO_SNDTIMEO, "\36\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0", 16) = 0
39579 fcntl(10, F_GETFL)                = 0x2 (flags O_RDWR)
39579 fcntl(10, F_SETFL, O_RDWR|O_NONBLOCK) = 0
39579 getpeername(10, {sa_family=AF_INET, sin_port=htons(26949), sin_addr=inet_addr("127.0.0.1")}, [16]) = 0
39579 epoll_ctl(6, EPOLL_CTL_ADD, 10, {EPOLLIN, {u32=10, u64=10}}) = 0
39579 epoll_wait(6, [{EPOLLIN, {u32=10, u64=10}}], 32, -1) = 1
39579 recvfrom(10, "", 1, 0, NULL, NULL) = 0
39579 epoll_ctl(6, EPOLL_CTL_DEL, 10, 0x7fff585ad4a0) = 0
39579 close(10)                         = 0
39579 epoll_wait(6, [{EPOLLIN, {u32=9, u64=9}}], 32, -1) = 1
```

TCP握手成功之后，客户端直接发送RST包断开TCP连接(阿里云负载均衡服务TCP端口健康检查)，socket_getpeername()函数会抛出异常

## socket_read 非阻塞模式 EAGAIN处理不正确

非阻塞模式下，我们对EAGAIN处理不正确造成（Resource temporarily unavailable）我们继续读时，造成段错误（SIGSEGV 段非法错误 (内存引用无效)​）worker exit，发送RST

由于我们实现的read 错误的返回了false thrift_protocol_read_binary_after_message_begin() 段错误，需改为空字符

## 阿里云redis lua 检查的问题

HMSET key field value [field value …]

因为直接使用table先组装好了field value
执行 script load 脚本时，会检查不成功，从而load失败，因为它判断这里少了一个参数
```
redis.call('hmset', KEYS[1], unpack(data))
```
