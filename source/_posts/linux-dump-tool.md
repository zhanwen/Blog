---
title: linux抓包工具
date: 2017-11-21 22:50:05
tags:
	- Linux
---
linux 抓包工具

	1、tcpdump系统自带抓包工具	
	
	//-nn表示让第三列和第四列显示成ip+端口号的实行。 
	//-i 后跟设备名
	2、tcpdump -nn -i eth0 tcp and host 192.168.0.1 and port 80
	
	vs0不限定包的长度
	-c 只抓多少包
	-w更文件，抓的包写到的文件
	3、tcpdump -nn -vs0 tcp and port not 22 -c 100 -w 1.cap


4、wireshark 在linux下也可以安装
	
	yum install -y wireshark

5、抓包分析http请求：
	
	tshark -n -t a -R http.request -T fields -e "frame.time" -e "ip.src" -e "http.host" -e "http.request.method" -e "http.request.ri"
抓取之后，会有一个文件，在 /tmp/etherXXXXevWMie 每次抓取之后最后最好删除，

	/tmp/etherXXXXevWMie |xargs rm
