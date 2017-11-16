---
title: Linux下安装一个源码包
date: 2017-11-16 21:05:33
tags:
	
	- Linux
---

源码包是开源的可自行更改的程序包，大多用用C语言开发，不能直接使用，需要编译成二进制的可执行文件，编译源码包的必须有gcc支持，如果没有需要安装
	
	yun install -y gcc

通常情况编译三步曲
	
	./configure		//配置各种编译参数 
	make			//根据指定的编译参数进行编译
	make install 	//安装到指定目录。

源码安装实例-httpd的源码安装
下载源码包
	
	cd /usr/local/src/ #约定目录
	wget http://apache.etoak.com/httpd/httpd-2.2.24.tar.bz2

解压
	
	tar -jxvf httpdd-2.2.24.tar.bz2 	//查看README或者INSTALL说明文件
<!-- more -->
指定编译参数  
	
	./configure --help

安装约定
	
	./configure --prefix=/usr/local/apache2

	echo $? 	//验证是否成功,如果输出结果是0，则上述的步骤执行成功

	make

	make install 或者以上两步可以一起执行	make & make install












