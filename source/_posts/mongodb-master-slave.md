---
title: mongodb主从复制集群
date: 2017-11-12 22:25:49
tags:
	- MongoDB
	- 数据库
---

主从复制是MongoDB最常用的复制方式。这种方式非常灵活，可用于备份、故障恢复、读扩展等。最基本的设置方式就是建立一个主节点和一个或者多个从节点，每个从节点要知道主节点的地址。
	
	//启动主服务器
	mongod --master
	//启动从服务器.其中master_address就是上面主节点的地址。
	mongod --slave --source master_address

主从复制主要是用来分流的，读写分离。比如写、改、更新操作在master上，读操作只在slave上。
