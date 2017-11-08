---
title: 更改MySQL数据库root的密码
date: 2017-11-08 22:41:23
tags:
	- Mysql
	- 数据库
---

默认root密码是空的，可以直接登录

	PATH=$PATH:/usr/local/mysql/bin

加入到/etc/profile
然后执行以下命令，使命令生效
	
	source /etc/profile

mysqladmin修改密码使用
	
	mysqladmin -uroot password ‘yourpass’ //设置密码

更改root密码
	
	mysqladmin -uroot -p password ‘newpass’

连接mysql
	
	mysql -uroot -p -h ip -Pport

