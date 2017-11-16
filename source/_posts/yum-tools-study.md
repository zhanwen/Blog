---
title: yum工具的使用
date: 2017-11-16 20:55:51
tags:
	- Linux
	- yum
---
# yum常用操作
	
	//wc -l 统计行数
	yum list |wc -l
	
	yum list 列出所有可用rpm包资源

搜索某个包

	yum search ‘keywords’ or yum list |grep ‘keywords’

yum安装包
	
	yum install -y filename（包名）
<!-- more -->
yum卸载包

	yum remove -y filename（包名）

yum升级包
	
	yum update -y filename（包名）

创建本地的yum源

	1. mount /dev/cdrom /mnt
	2. cp -f /etc/yum.repos.d /etc/yum.repos.d.bak
	3. rm -rf /etc/yum.repos.d/*
	4. vim /etc/yum.repos.d/dvd.repo #加入如下内容
		[dvd] 
		name=install dvd
		baseurl=file:///mnt 
		enabled=1 
		gpgcheck=0
	5. yum makecache

利用yum下载一个rpm包

	yum install -y yum-plugin-downloadonly.noarch

首先需要安装一个插件来支持只下载不安装

	yum install 包名 -y --downloadonly #这样就已经下载了

	yum install 包名 -y --downloadonly --downloaddir=/usr/local/src #指定一个下载目录



