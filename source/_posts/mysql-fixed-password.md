---
title: 更改MySQL数据库密码以及忘记密码修改
date: 2017-11-08 22:41:23
tags:
	- Mysql
	- 数据库
---
修改Mysql的root账户密码，默认root密码是空的，可以直接登录

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

<!-- more -->
如果忘记了mysql密码，可以使用下面的方式来重新设置密码，编辑mysql主配置文件my.cnf，在[mysqld]字段下添加参数skip-grant，重启数据库服务

	service mysqld restart

修改相应用户密码
	
	use mysql；
	update user set password=password('yourpassword') where user='root';

修改/etc/my.cnf 去掉skip-grant，重启mysql