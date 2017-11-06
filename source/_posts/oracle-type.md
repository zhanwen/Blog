---
title: oracle数据类型
date: 2017-11-06 22:27:11
tags:
	- Oracle
	- 数据库
---
SQL的类型
	
	1. DML(Data Manipulation Language 数据操作语言): insert update delete select
	2. DDL(Data Definition Language 数据定义语言): 
			create/alter/drop/truncate tabl
			create/drop view,sequence,index,synonym(同义词)
	3. DCL(Data Control Language 数据控制语言）： grant(授权) revoke（撤销权限）
<!-- more -->
delete 和truncate区别
	
	1. delete 逐条删除，truncate先删除表，再重建
	2. delete是DML(可以回滚) truncate是DDL（不可以回滚）
	3. delete 不会释放空间；truncate会	//delete 并不是真正删除；把数据换个地方(undo 表空间)存
	4. delete会产生碎片  truncate不会
	5. delete可以闪回(flashback) truncate不可以

	
set feedback off 回显信息关闭

海量插入数据
	
	1. 数据泵
	2. SQL*Loader
	3. 外部表

	
去除碎片
	
	1、alter table 表名 move
	2、导出导入

Oracle中的事务
	
	1. 起始标志：事务中第一条DML语句
	2. 结束标志： 提交： 显式 commit
						隐式 正常退出exit，DDL，DCL
				回滚   显式 rollback
						隐式 非正常退出，掉电，宕机
						

