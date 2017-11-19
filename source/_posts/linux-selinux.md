---
title: linux防火墙selinux
date: 2017-11-19 22:36:21
tags:
	- Linux
---

1、是Redhat/CentOS系统特有的安全机制。不过因为这个东西限制太多，我们可以不启用。

2、查看selinux的状态的命令：

	（1）getenforce 设置为setenforce
	（2）sestatus

3、配置文件/etc/selinux/config 三种形式：enforcing，permissive，disabled

	SELINUX=disabled
<!-- more -->
4、没有这两个命令setenforce 0/1 getenforce 用一下命令安装

	yum install -y libselinux-utils

iptables

	1、iptables -nvL 查看规则
	2、iptables -F 清除当前的规则
	3、iptables -Z 计数器清零
	4、service iptables save 保存规则 保存的规则文件为：/etc/sysconfig/iptables
	5、service iptables stop 可以暂停防火墙，但重启之后它会读取/etc/sysconfig/iptables从而启动防火墙，另外即使我们停止防火墙，但一旦我们添加任何一条规则，它也会开启
