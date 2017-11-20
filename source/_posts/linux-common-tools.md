---
title: linux常用工具ps、free、netstat
date: 2017-11-20 23:24:31
tags:
	- Linux
---
# PS 命令
> 1、ps aux /ps -elf
2、PID：进程的id，这个id很有用，在linux中内核管理进程就得靠pid来识别和管理某一个进程，比如我想终止某一个进程，则用‘kill 进程的pid’有时并不能杀掉，则需要加一个-9选项了，kill -9 进程pid
3、STAT：表示进程的状态，进程状态分为以下几种
4、D不能中断的进程（通常为IO）
5、R正在运行中的进程
6、S已经中断的进程，系统大部分进程都是这个状态
7、T已经停止或者暂停的进程，如果我们正在运行一个命令，比如说sleep 10 如果我们按一下ctrl -z 让他暂停，那么我们用ps查看就会显示T这个状态
8、X已经死掉的进程（这个从来不会出现）
9、Z表示僵尸进程，即杀不掉、打不死的垃圾进程，占系统一点资源，不过没有关系。如果占用太多（一般不会出现），就需要重视了。
10、< 高优先级进程
11、N 低优先级进程
12、L在内存中被锁了内存分页
13、s主进程
14、l多线程进程
15、+在前台运行的进程

重新加载进程
	kill -HUP pid

<!-- more -->
# free 查看系统内存使用情况
>1、free以k为单位显示-m以M为单位 -g以G为单位
2、mem（total）：内存总数；
mem（used）：已经分配的内存
mem（free)：未分配的内存；
mem（buffers）：系统分配但未被使用的buffers；
mem（cached）系统分配但未被使用的cache
3、buffers/cache（used）：实际使用的buffers与cache总量，也是实际使用的内存；buffers/cache（free）：未被使用的buffers与cache和未被分配的内存值和，这就是系统当前实际可用内存
4、buffers是即将要被写入磁盘的，cache是被从磁盘中读出来的

# netstat 查看网络状况
> 1、netstat -lnp 查看当前系统开启的端口以及socket
2、netstat -an 查看当前系统所有的连接
3、关于tcp三次握手和四次挥手的文章
