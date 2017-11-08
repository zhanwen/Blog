---
title: Mysql数据库常用操作
date: 2017-11-08 22:23:06
tags:
	- Mysql
	- 数据库
---

查看都有哪些库
	
	show databases;

查看某个库的表
	
	use db； 
	show tables；

查看表的字段
	
	desc tb；

查看建表语句
	
	show create table tb；

在结尾加上\G格式化一下，比较容易观察。

<!-- more -->

当前是哪个用户
	
	select user（）；

当前库

	select database（）；

创建库
	
	create datebase db1；

创建表
	
	create table t1 (`id` int(4), `name` char(40));

查看数据库版本
	
	select version();

查看mysql状态
	
	show status;

修改mysql参数
	
	show variables like 'max_connect%';

可以在/etc/my.cnf里面修改

	set global max_connect_errors = 1000;
	mysql -uroot -p654321 -hlocalhost -P3306 -e ‘show processlist’

查看mysql队列
	
	show processlist;

创建普通用户并授权
	
	grant all on *.* to user1 identified by '123456';
	grant all on db1.* to 'user2'@'10.0.2.100' identified by '111222';
	grant all on db1.* 'user3'@'identified by '231222';

更改密码
	
	UPDATE mysql.user SET password=PASSWORD("newpwd")WHERE user='username';

刷新缓存

	flush privileges;









两种引擎的区别
Myisam，innodb