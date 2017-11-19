---
title: linux下iptables的三张表
date: 2017-11-19 22:39:19
tags:
	- Linux
	- iptables
---

1、iptables -t 指定表名，默认不加-t则是filter表
2、filter 这个表主要用于过滤包的，是系统预设的表，内建三个链INPUT、OUTPUT以及FORWARD。INPUT作用于进入本机的包：OUTPUT作用于本机送出的包；FORWARD作用于那些跟本机无关的包。
3、nat主要用处是网络地址转换，也有三个链。PREROUTING链的作用是在包刚刚到达防火墙时改变它的目的地址，如果需要的话。OUTPUT链改变本地产生的包的目的地址。POSTROUTING链在包就要离开防火墙之前改变其源地址。
4、mangle这个表主要是用于给数据包打标记，然后根据标记去操作哪些包。这个表几乎不怎么用。
<!-- more -->
iptables规则：

> 1、查看规则iptables -t nat -nvL
2、清楚规则iptables -t nat -F
3、增加/删除规则
iptables -A/-D INPUT -s 10.72.11.12 -p tcp --sport 1234 -d 10.72.137.159 --dport 80 -j DROP
4、插入规则
iptables -I INPUT -s 1.1.1.1 -j DROP/ACCEPT/REJECT
5、iptables -nvL --line-numbers 查看规则带有id号
6、iptables -D INPUT 1 根据规则的id号删除对应规则
7、iptables -P INPUT DROP 用来设定默认规则，默认是ACCEPT，一旦设定为DROP后，只能使用iptables -P ACCEPT 才能恢复成原始状态，而不能使用-F参数

iptables实例

1、针对filter表，预设策略INPUT链DROP，其他两个链ACCEPT，然后针对192.168.137.0/24开通22端口，对所有网段开发80端口，对所有网段开放21端口。脚本如下：
```
#!/bin/bash
ipt="/sbin/iptables"
$ipt -F;$ipt -P INPUT DROP;
$ipt -P OUPUT ACCEPT; $ipt -P FORWARD ACCEPT;
$ipt -A INPUT -s 192.18.137.0/24 -p tcp --dport 22 -j ACCEPT
$ipt -A INPUT -p tcp --dport 80 -j ACCEPT $ipt -A INPUT -p tcp --dport 21 -j ACCEPT
```

2、icmp的包有常见的应用，本机ping通外网，外网ping不通本机

	iptables -I INPUT -p icmp --icmp-type 8 -j DROP

iptables nat表应用：
> 1、路由器就是使用iptables的nat原理实现
2、假设您的机器上有两块网卡eth0和eth1，其中eth0的IP为192.168.10.11,eth1的IP为172.16.10.11。eth0连接了intnet但eth1没有连接，现在有另一台机器（172.16.10.12）和eth1是互通的，那么如何设置也能够让连接eth1的这台机器能够连接intnet？
3、echo "1" >／proc/sys/net/ipv4/ip_forward
4、iptables -t nat -A POSTROUTING -s 172.16.10.0/24 -o eth0 -j MASQUERADE

iptables规则备份与恢复
> 1、service iptables save 这样会保存到/etc/sysconfig/iptables
2、iptables-save >ｍyipt.rule 可以把防火墙规则保存到指定文件中
3、iptables-restore < myipt.rule 这样可以恢复指定的规则







