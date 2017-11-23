---
title: linux系统日志
date: 2017-11-23 22:52:22
tags:
	- Linux
	- Log
---
Linux系统日志学习笔记
> 1、/var/log/messages核心系统日志文件
2、每周归档一个日志messages-20150909
3、/etc/logrotate.conf
4、messages由syslogd这个守护进程产生的，如果停掉这个服务则系统不会产生/var/log/messages
5、/var/log/wtmp 查看用户登录历史last
6、/var/log/btmp lastb 查看无效登录历史
7、/var/log/maillog 
8、/var/log/secure
9、dmesg
10、/var/log/dmesg

<!-- more -->
exec与xargs
1、exec和find同时使用
2、查找当前目录创建时间大于10天的文件并删除
	
	find . -mtime +10 -exec rm -rf {} \;

3、批量更改文件名
	
	find ./* -exec mv {} {}_bak \;

4、xargs用在管道符号后面

5、
	
	find . -mtime +10 |xargs rm -rf

6、
	
	ls -d ./* |xargs -n1 -i{} mv {} {}_bak

7、xargs可以把多行变成一行 
	
	cat 1.txt|xargs

screen工具介绍
> 1、screen相当于一个虚拟终端，它不会因为网络中断而退出，每次登录都可以进入那个screen
使用方法：直接输入screen
2、screen -ls 查看已经开启的screen
3、ctrl+a 再按d退出该screen会话，知识退出，并没有结束，结束的话输入ctrl+d或者输入exit
4、退出后还想再次登录某个screen会话，使用screen -r screenid若只有一个screen直接screen -r
5、screen -S hanwen ；登录的话screen -r hanwen
