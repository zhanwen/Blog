---
title: linux系统服务管理和系统任务计划
date: 2017-11-21 22:56:57
tags:
	- Linux
---
Linux系统服务管理
1、工具ntsysv类似图形界面管理工具，如果没有该命令使用下面命令安装
	
	yum install -y ntsysv

2、常用的服务有
	
	crond，iptables，network，sshd，rsyslog，sysstat，irqbalance，sendmail，microcode_ctl

3、列表
	
	chkconfig --list

4、添加或者删除一个

	chkconfig --add/del servicename

5、状态开关

	chkconfig --level[345] servicename on/off
<!-- more -->

1、通过 crontab 这个命令来完成的常用的选项有

	分 时 日 月 周
	-u：指定某个用户，不加-u选项则为当前用户；
	-e：制定计划任务
	-l：列出计划任务
	-r：删除计划任务
	
用户的cron存到了这里/var/spool/cron/username
