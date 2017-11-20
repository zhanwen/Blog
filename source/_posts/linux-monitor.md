---
title: linux系统监控
date: 2017-11-20 23:15:30
tags:
	- Linux
	- System
---
在使用监控之前可能会没有安装该命令，如果没有安装可以使用下面的命令进行安装
	
	yum install -y sysstat

网卡流量sar -n DEV，
	
	sar -n DEV 1 10

查看历史负载
	
	sar -q

查看磁盘读写

	sar -b
<!-- more -->
	
	1、命令w和uptime的第一行，upload

	2、system load averages 单位时间短内活动的进程数

	3、查看cpu的个数和核数
	processor     physical id 

	4、vmstat

	5、vmstat 1 10
	1分钟      5分钟     15分钟


查看cpu信息
	
	cat /proc/cpuinfo

proc显示进程的相关信息
> r：表示允许和等待cpu时间片的进程数，如果长期大于服务器cpu的个数，则说明cpu不够用了。
b：表示等待资源的进程数，比如等待I/O、内存等。该数值如果长时间大于11，则需要关注一下

swap：
> si：表示由交换区写入到内存的数据量
so：表示由内存写入到交换区的数据量

io：
> bi：表示从块设备读取数据的量（读磁盘）
bo：表示从块设备写入数据的量（写磁盘）

wa：表示I/O等待所占用的CPU的时间百分比,查看io还有iotoo这个工具,没有可以安装
	
	yum install -y iotop

top
> top用于动态监控进程所占系统资源，每隔3秒变一次。RES这一项为进程所占内存大小，而%MEM为使用内存百分比。在top状态下，按“shift + m”，可以按照内存使用大小排序。按数字‘1’可以列出各颗cpu的使用状态
top -bn1 它表示非动态打印系统资源使用情况，可以用在shell脚本中。







