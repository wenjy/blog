---
url: /2020/03/26/linux-command.html
title: "Linux 常用命令"
keywords: "Linux 常用命令"
description: "Linux 常用命令"
date: 2020-03-26T22:53:12+08:00
draft: false
tags: ["Linux"]
tags_weight: 100
categories: ["Linux"]
categoryes_weight: 100
top: true
---

## ls

`ls -alh` 

> -a, -all 列出目录下的所有文件，包括以 . 开头的隐含文件
-l 除了文件名之外，还将文件的权限、所有者、文件大小等信息详细列出来
-h, –human-readable 以容易理解的格式列出文件大小 (例如 1K 234M 2G)

## cd

`cd /usr/local` 使用绝对路径

`cd ../` 使用相对路径

`cd ~` home目录

`cd -` 返回进入此目录之前所在的目录

## pwd
查看“当前工作目录”的完整路径

`pwd -P`  显示出实际路径，而非使用连接（link）路径

`pwd -L` 目录连接链接时，输出连接路径

## rm
> 删除一个目录中的一个或多个文件或目录，它也可以将某个目录及其下的所有文件及子目录均删除。
对于链接文件，只是删除了链接，原有文件均保持不变。

`rm -rf /a/b/c` 我是谁，我在哪里，我做了什么？

`rm -i /a/b/*.log` 可以使用通配符

> -f, --force 忽略不存在的文件，从不给出提示。
-i, --interactive 进行交互式删除
-r, -R, --recursive 指示rm将参数中列出的全部目录和子目录均递归地删除。

## rmdir
删除空目录，一个目录被删除之前必须是空的。

## mv
mv命令是move的缩写，可以用来移动文件或者将文件改名。

`mv test.log test1.txt` 把test.log重命名为test1.txt

## cp
复制文件或者目录

`cp log1 log.bak` 复制log1为log.bak

## cat
显示文件内容，或者将几个文件连接起来显示，或者从标准输入读取内容并显示，它常与重定向符号配合使用。

`cat filename` 显示filename的内容

`cat filename1 fielname2 > filename3` 把filename1和2合并为filename3，只能创建新文件,不能编辑已有文件

## less
文件或其它输出进行分页显示，文件比较大时less不是加载整个文件

## head
显示开头

`head -n 10 filename` 显示前10行

## tail
显示结尾

`tail -n 10 -f filename` 循环显示结尾10行，调试监控某日志文件时经常使用

## which
查看可执行文件的位置。
在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。

## whereis 
查看文件的位置。定位可执行文件、源代码文件、帮助文件在文件系统中的位置。
 
## locate   
配合数据库查看文件位置。loacte时直接找该索引，查询速度会较快，索引数据库一般是由操作系统管理

## find   
实际搜寻硬盘查询文件名称。

`find . -type f -exec ls -l {} \;` ls -l命令放在find命令的-exec选项中

`find . -type f -print | xargs file` 查找系统中的每一个普通文件，然后使用xargs命令来测试它们分别属于哪类文件

`find . -name "*.log" -print` 在当前目录及子目录中查找所有的‘*.log’文件

## chomd
改变linux系统文件或目录的访问权限。

`chomd -R 0644 /test` 递归修改目录权限

`chomd +x /test/test.sh` 增加执行权限

## tar
压缩，解压缩，打包，解包等

`tar xvf FileName.tar` 解包

`tar cvf FileName.tar DirName` 打包

`tar zxvf FileName.tar.gz` 解压

`tar zcvf FileName.tar.gz DirName` 压缩

`tar jxvf FileName.tar.bz2` 解压

`tar jcvf FileName.tar.bz2 DirName` 压缩

## chown
将指定文件的拥有者改为指定的用户或组，用户可以是用户名或者用户ID；组可以是组名或者组ID；
文件是以空格分开的要改变权限的文件列表，支持通配符

`chown mail:mail -R /test` 递归把`/test` 目录的用户和用户组改成mail

## df
检查linux服务器的文件系统的磁盘空间占用情况。

`df -h` 方便阅读方式显示

## du
文件和目录磁盘使用的空间的查看

`du -h  --max-depth=1` 输出当前目录下各个子目录所使用的空间

## ln
为某一个文件在另外一个位置建立一个同步的链接.当我们需要在不同的目录，用到相同的文件时，
我们不需要在每一个需要的目录下都放一个必须相同的文件，我们只要在某个固定的目录，放上该文件，
然后在 其它的目录下用ln命令链接（link）它就可以，不必重复的占用磁盘空间

`ln -s log2013.log link2013` 给文件log2013.log创建软链接

## diff
用于比较文件的内容，特别是比较两个版本不同的文件以找到改动的地方

`diff log2014.log log2013.log`

## date 
可以用来显示或设定系统的日期与时间

`date '+%Y-%m-%d %T'` 2020-03-26 15:26:08

## grep
(Global Regular Expression Print)使用正则表达式搜索文本，并把匹 配的行打印出来。

`ll /usr/local/bin |grep php`

## wc
(Word Count)统计指定文件中的字节数、字数、行数，并将统计结果显示输出

`ps aux|grep php-fpm|wc -l` 统计php-fpm的进程数

## ps

`ps aux|grep php-fpm`

解释：

USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND

USER 进程用户 

PID 进程ID
 
%CPU 进程占用的CPU百分比
 
%MEM 占用内存的百分比 

VSZ 进程使用的虚拟内存量（KB） 

RSS 进程占用的固定内存量（KB）

TTY 该进程在哪个终端上运行（登陆者的终端位置），若与终端无关，则显示（？）。若为pts/0等，则表示由网络连接主机进程

STAT 当前状态（D 无法中断的休眠状态、R 正在运行、S 处于休眠、T 停止或被追踪、Z 僵尸进程、< 优先级高、N 优先级较低、s 进程的领导者、l 多进程、+ 位于后台的进程组）

START 进程被触发启动时间

TIME 进程实际使用CPU运行的时间

COMMAND 命令的名称和参数
`

`ps -ef|grep php-fpm`

## kill
用来终止指定的进程

`kill -9 12345` 强制杀死进程

`kill -15 12345` 终止指定进程

## killall
用于杀死指定名字的进程（kill processes by name）

`killall php-fpm`

## pkill
pkill和killall应用方法差不多，也是直接杀死运行中的程序；如果想杀掉单个进程，请用kill来杀掉。

`软件能用自己提供的命令来终止，重启最好`

## top
实时显示系统中各个进程的资源占用状况

## uptime
top中运行时间，负载值

## free
显示Linux系统中空闲的、已用的物理内存及swap内存,及被内核使用的buffer

`free -m`

## vmstat
Virtual Meomory Statistics（虚拟内存统计）的缩写，可对操作系统的虚拟内存、进程、CPU活动进行监控

## watch
watch可以帮你监测一个命令的运行结果，省得你一遍遍的手动运行。在Linux下，watch是周期性的执行下个程序，并全屏显示执行结果

`watch -n 1 -d netstat -ant` 每隔一秒高亮显示网络链接数的变化情况

## crontab

`crontab -u www -l` 查看

`crontab -e` 编辑

## ifconfig
命令用来查看和配置网络设备。当网络环境发生改变时可通过此命令对网络进行相应的配置

`ifconfig eth0 up` 启动网卡

`ifconfig eth0 down` 关闭网卡

## route
用于显示和操作IP路由表（show / manipulate the IP routing table）

## ping
测试与目标主机的连通性

`ping www.baidu.com`

`ping 112.112.112.112`

## traceroute
通过发送小的数据包到目的设备直到其返回，来测量其需要多长时间。一条路径上的每个设备traceroute要测3次。
输出结果中包括每次测试的时间(ms)和设备的名称（如有的话）及其IP地址。

`traceroute www.baidu.com`

## netstat
显示与IP、TCP、UDP和ICMP协议相关的统计数据，一般用于检验本机各端口的网络连接情况。
netstat是在内核中访问网络及相关信息的程序，它能提供TCP连接，TCP和UDP监听，进程内存管理的相关报告。

`netstat -anlp |grep :9999`

`netstat -s` 统计各网络信息

## ss
Socket Statistics的缩写。顾名思义，ss命令可以用来获取socket统计信息，它可以显示和netstat类似的内容。
但ss的优势在于它能够显示更多更详细的有关TCP和连接状态的信息，而且比netstat更快速更高效。

`ss -anlp|grep :9999`

## telnet
用来远程登录

`telnet 127.0.0.1 9999`

## rcp
用于在计算机之间拷贝文件。rcp命令有两种格式。第一种格式用于文件到文件的拷贝；第二种格式用于把文件或目录拷贝到另一个目录中。

`rcp test1 webserver1:/home/root/test3` 将当前目录下的 test1 复制到名为 webserver1的远程系统

## scp
secure copy的简写，用于在Linux下进行远程拷贝文件的命令，和它类似的命令有cp，不过cp只是在本机进行拷贝不能跨服务器，而且scp传输是加密的。

`scp local_file remote_username@remote_ip:remote_folder` 从本地服务器复制到远程服务器

`scp root@192.168.120.204:/opt/soft/nginx-0.5.38.tar.gz /opt/soft/` 从远程服务器复制到本地服务器

## wget
一个下载文件的工具，它用在命令行下

`wget http://www.minjieren.com/wordpress-3.1-zh_CN.zip` 下载单个文件

`wget -O wordpress.zip http://www.minjieren.com/download.aspx?id=1080` 使用wget -O下载并以不同的文件名保存

`wget -c http://www.minjieren.com/wordpress-3.1-zh_CN.zip` 使用wget -c断点续传

`wget -c http://www.minjieren.com/wordpress-3.1-zh_CN.zip` 使用wget -b后台下载

## tcpdump
在调试网络通信程序是 tcpdump 是必备工具。tcpdump 很强大，可以看到网络通信的每个细节。
如 TCP，可以看到 3 次握手，PUSH/ACK 数据推送，close4 次挥手，全部细节。包括每一次网络收包的字节数，时间等。

`tcpdump -i lo -Xnlps0 tcp port 9999` 抓lo网卡，tcp协议，端口9999 通信的数据内容

`tcpdump host 127.0.0.1` 指定IP

[命令详解](https://www.cnblogs.com/ggjucheng/archive/2012/01/14/2322659.html)

## strace
可以跟踪系统调用的执行情况，在程序发生问题后，可以用 strace 分析和跟踪问题。

`strace -o /tmp/strace.log -f -p $PID`

## gdb
GDB 是 GNU 开源组织发布的一个强大的 UNIX 下的程序调试工具，可以用来调试 C/C++ 开发的程序，PHP 和 Swoole 是使用 C 语言开发的，
所以可以用 GDB 来调试 PHP+Swoole 的程序。
gdb 调试是命令行交互式的，需要掌握常用的指令。

`gdb -p 进程ID`

## lsof
可以查看某个进程打开的文件句柄。可以用于跟踪 swoole 的工作进程所有打开的 socket、file、资源。

`lsof -i:9999`

`lsof -p [进程ID]`

## pref
非常强大的动态跟踪工具，perf top 指令可用于实时分析正在执行程序的性能问题。
与 callgrind、xdebug、xhprof 等工具不同，perf 无需修改代码导出 profile 结果文件。

`perf top -p [进程ID]`

## awk
一个数据处理工具

`awk '条件类型1'{动作1} 条件类型2{动作2}... filename` 

```bash
# 变量名称
# $0 代表一整行数据
# $1-$n 代表第几列数据
# NF 每一行拥有的字段总数（$0）
# NR 目前 awk 处理的是 第几行 数据
# FS 目前的分隔符，默认是空格键

# 处理流程
# 1、读入第一行，并将第一行数据填入$0,$1,$2..$n变量中
# 2、依据条件类型的限制，判断是否要进行后面的动作
# 3、做完所有的动作与条件类型
# 4、若后面还有行数据，则重复1、2、3，直到所有的行读取完毕

```

`cat /etc/passwd |awk 'BEGIN {FS=":"} $3 < 10 {print $1 "\t" $3}'`

## groups
查看有效与支持的用户组

## useradd
新增用户

`userass test_user`

## passwd
设置修改用户密码

`passwd test_user`

## su
切换用户身份，需要输入切换用户的密码

`su username` 切换成username用户，读取变量的设置方式为`non-login`shell的方式，这样方式下很多原本的环境变量不会被改变

`su - username` 读取变量的设置方式为`login`shell的方式

`su -c username "cmd"` 代表仅用此用户执行一次命令

## sudo
切换用户，输入自己的密码，需要配置 `/ect/sudoers`，可用`visudo`命令编辑

`sudo -u username pwd` 不加用户参数，默认root

## curl
基于网络协议，对指定URL进行网络传输
curl支持的通信协议有DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SBMS, SMTP, SMTPS, TELNET 和TFTP

`curl --trace-time -v https://www.baidu.com` 可以调试请求时间和具体过程

## ssh
SSH是一种网络协议，用于计算机之间的加密登录

`ssh-keygen` 创建公钥

`ssh-copy-id user@host` 复制本机公钥到服务器

`ssh -p 2222 user@host` 登录服务器

### 用来传输数据

`cd && tar czv src | ssh user@host 'tar xz'` 将$HOME/src/目录下面的所有文件，复制到远程主机的$HOME/src/目录

`ssh user@host 'tar cz src' | tar xzv` 将远程主机$HOME/src/目录下面的所有文件，复制到用户的当前目录

### 绑定本地端口

可以让那些不加密的网络连接，全部改走SSH连接，从而提高安全性

`ssh -D 8080 user@host` 让8080端口的数据，都通过SSH传向远程主机

SSH会建立一个socket，去监听本地的8080端口。一旦有数据传向那个端口，就自动把它转移到SSH连接上面，发往远程主机。
可以想象，如果8080端口原来是一个不加密端口，现在将变成一个加密端口

### 本地端口转发

有时，绑定本地端口还不够，还必须指定数据传送的目标主机，从而形成点对点的"端口转发"。
为了区别后文的"远程端口转发"，我们把这种情况称为"本地端口转发"（Local forwarding）。

假定host1是本地主机，host2是远程主机。由于种种原因，这两台主机之间无法连通。但是，另外还有一台host3，可以同时连通前面两台主机。
因此，很自然的想法就是，通过host3，将host1连上host2

`ssh -L 2121:host2:21 host3`

命令中的L参数一共接受三个值，分别是"本地端口:目标主机:目标主机端口"，它们之间用冒号分隔。
这条命令的意思，就是指定SSH绑定本地端口2121，然后指定host3将所有的数据，转发到目标主机host2的21端口（假定host2运行FTP，默认端口为21）
这样一来，我们只要连接host1的2121端口，就等于连上了host2的21端口

"本地端口转发"使得host1和host3之间仿佛形成一个数据传输的秘密隧道，因此又被称为"SSH隧道"

### 远程端口转发

既然"本地端口转发"是指绑定本地端口的转发，那么"远程端口转发"（remote forwarding）当然是指绑定远程端口的转发。

还是接着看上面那个例子，host1与host2之间无法连通，必须借助host3转发。但是，特殊情况出现了，host3是一台内网机器，它可以连接外网的host1，但是反过来就不行，外网的host1连不上内网的host3。这时，"本地端口转发"就不能用了，怎么办？

解决办法是，既然host3可以连host1，那么就从host3上建立与host1的SSH连接，然后在host1上使用这条连接就可以了

`ssh -R 2121:host2:21 host1`

R参数也是接受三个值，分别是"远程主机端口:目标主机:目标主机端口"。
这条命令的意思，就是让host1监听它自己的2121端口，然后将所有数据经由host3，转发到host2的21端口。由于对于host3来说，host1是远程主机，所以这种情况就被称为"远程端口绑定"

`ssh -NT -f -D 8080 host`

N参数，表示只连接远程主机，不打开远程shell；T参数，表示不为这个连接分配TTY。这个两个参数可以放在一起用，代表这个SSH连接只用来传数据，不执行远程操作
f参数，表示SSH连接成功后，转入后台运行。这样一来，就可以在不中断SSH连接的情况下，在本地shell中执行其他操作

## netcat

调试tcp udp

`netcat -u ip port` 调试udp

## time

显示命令执行时间

`time command`

参考链接：

[每天一个Linux命令](https://www.cnblogs.com/peida/category/309012.html)

[Swoole文档 工具使用](https://wiki.swoole.com/#/other/tools)

[阮一峰 SSH原理与运用（二）：远程操作与端口转发](http://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html)
