---
title: Linux下安装Tomcat
date: 2017-12-22 22:38:15
tags:
	- Tomcat
	- Linux
---
`Tomcat`官网 [点击这里](http://tomcat.apache.org)

	cd /usr/local/src/

	wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.24/bin/apache-tomcat-8.5.24.tar.gz

	tar -zxvf apache-tomcat-8.5.24.tar.gz

	mv apache-tomcat-8.5.24.tar.gz /usr/local/tomcat

	cp -p /usr/local/tomcat/bin/catalina.sh /etc/init.d/tomcat

	vim /etc/init.d/tomcat //第二行加入
	# chkconfig: 112 63 37
	# description: tomcat server init script
	# Source Function Library
	. /etc/init.d/functions
	JAVA_HOME=/usr/local/jdk1.8.0_23/
	CATALINA_HOME=/usr/local/tomcat

<!-- more -->

	chmod 755 /etc/init.d/tomcat

	chkconfig --add tomcat

	chkconfig tomcat on

	service tomcat start

	ps aux |grep tomcat

浏览器输入http://[自己的ip]:8080 可以看到tomcat的欢迎页

