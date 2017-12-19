---
title: pig使用笔记
date: 2017-12-19 23:32:41
tags:
	- Pig
	- 大数据
---
### 1. 安装Pig

>	将pig添加到环境变量当中
	
### 2. pig使用

>	首先将数据库中的数据导入到HDFS上
	
	sqoop import --connect jdbc:mysql://192.168.1.10:3306/yun --username root --password 123  --table trade_detail --target-dir '/sqoop/td'
	sqoop import --connect jdbc:mysql://192.168.1.10:3306/yun --username root --password 123  --table user_info --target-dir '/sqoop/ui'
<!-- more -->
	

> td = load '/sqoop/td' using PigStorage(',') as (id:long, account:chararray, income:double, expenses:double, time:chararray);
> ui = load '/sqoop/ui' using PigStorage(',') as (id:long, account:chararray, name:chararray, age:int);

	
	td1 = foreach td generate account, income, expenses, income-expenses as surplus;

	
	td2 = group td1 by account;

	
	td3 = foreach td2 generate group as account, SUM(td1.income) as income, SUM(td1.expenses) as expenses, SUM(td1.surplus) as surplus;

	
	tu = join td3 by account, ui by account;

	
	result = foreach tu generate td3::account as account, ui::name, td3::income, td3::expenses, td3::surplus;

	
	store result into '/result' using PigStorage(',');