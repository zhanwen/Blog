---
title: hive学习笔记
date: 2017-10-28 19:34:11
tags:
	- Hive
	- 大数据
---

上传hive安装包

解压

配置

安装mysql

查询以前安装的mysql相关包			
	
	rpm -qa | grep mysql

<!-- more -->

暴力删除这个包
		
		rpm -e mysql-libs-5.1.66-2.el6_3.i686 --nodeps
		rpm -ivh MySQL-server-5.1.73-1.glibc23.i386.rpm 
		rpm -ivh MySQL-client-5.1.73-1.glibc23.i386.rpm

执行命令设置mysql
		
		/usr/bin/mysql_secure_installation

将hive添加到环境变量当中
		
		GRANT ALL PRIVILEGES ON hive.* TO 'root'@'%' IDENTIFIED BY '123' WITH GRANT OPTION;
		FLUSH PRIVILEGES

		
		create table trade_detail (id bigint, account string, income double, expenses double, time string) row format delimited fields terminated by '\t';
		create table user_info (id bigint, account string, name  string, age int) row format delimited fields terminated by '\t';

将mysq当中的数据直接导入到hive当中
		
		sqoop import --connect jdbc:mysql://192.168.1.10:3306/itcast --username root --password 123 --table trade_detail --hive-import --hive-overwrite --hive-table trade_detail --fields-terminated-by '\t'
		sqoop import --connect jdbc:mysql://192.168.1.10:3306/itcast --username root --password 123 --table user_info --hive-import --hive-overwrite --hive-table user_info --fields-terminated-by '\t'

创建一个result表保存前一个sql执行的结果
		
		create table result row format delimited fields terminated by '\t' as select t2.account, t2.name, t1.income, t1.expenses, t1.surplus from user_info t2 join (select account, sum(income) as income, sum(expenses) as expenses, sum(income-expenses) as surplus from trade_detail group by account) t1 on (t1.account = t2.account);
		
		create table user (id int, name string) row format delimited fields terminated by '\t'

将本地文件系统上的数据导入到HIVE当中
		
		load data local inpath '/root/user.txt' into table user;

创建外部表
		
		create external table stubak (id int, name string) row format delimited fields terminated by '\t' location '/stubak';
		
普通表和分区表区别：有大量数据增加的需要建分区表
		
		create table book (id bigint, name string) partitioned by (pubdate string) row format delimited fields terminated by '\t'; 

分区表加载数据
		
		load data local inpath './book.txt' overwrite into table book partition (pubdate='2010-08-22');

