---
title: rpm工具的使用
date: 2017-11-16 21:00:47
tags:
	- rpm
	- Linux
---
先介绍一下rpm包名字的构成，是由-和.分成了若干部分分，如：

	abrt-cli-2.0.8.15.el6.centos.i686.rpm

rpm包并没有写具体的平台而是noarch这代表这个rpm包没有硬件平台限制。

安装一个包

	-i表示安装
	-v可视化
	-h显示安装进度
	-force：强制安装，即使覆盖属于其它包的文件也要安装
	-nodeps：当要安装的rpm包依赖其他包时，即使其他包没有安装，也要安装这个包

	rpm -ivh /mnt/Package/libjpeg-turbo-devel-1.2.1-1.el6.i686.rpm

<!-- more -->
升级
	
	-U：就是升级的意思(update)		
	rpm -Uvh filename.rpm

rpm的卸载
	
	rpm -e filename

查询一个包是否安装
	
	rpm -q 包名（不带有平台信息以及后缀名）

查询当前系统所有安装过的rpm包

	rpm -qa 

查看是否安装过相关的,grep是过滤的作用
	
	rpm -qa |grep vim 

查询rpm包的相关信息

	rpm -qi 包名

列出一个rpm所安装的文件列表： 
	
	rpm -ql 包名

某个文件属于哪个rpm包，文件的绝对路径

	rpm -qf filename 

这里可以结合反引号一起使用，比如

	rpm -qf `which ls`  反引号是esc键下面的那个

查看该文件由哪个包所安装
















