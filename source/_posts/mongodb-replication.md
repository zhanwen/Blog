---
title: Mongodb副本集
date: 2017-11-12 22:33:24
tags:
	- MongoDB
	- 数据库
---

副本集就是有自动故障恢复功能的主从集群。主从集群和副本集最大的区别就是副本集没有固定的“主节点”；整个集群会选出一个“主节点”，当其挂掉后，又在剩下的从节点中选中其他节点为“主节点”，副本集总有一个活跃点(primary)和一个或多个备份节点(secondary)。下面以三个节点为例

	//节点1：
	HOST：localhost:10001
	Log File：D:\mongodb\logs\node1\logs.txt
	Data File：D:\mongodb\dbs\node1

	//节点2：
	HOST：localhost:10002
	Log File：D:\mongodb\logs\node2\logs.txt
	Data File：D:\mongodb\dbs\node2
<!-- more -->
	//节点3：
	HOST：localhost:10003
	Log File：D:\mongodb\logs\node3\logs.txt
	Data File：D:\mongodb\dbs\node3

	//启动节点1：
	mongod --dbpath D:\mongodb\dbs\node1 --logpath D:\mongodb\logs\node1\logs.txt --logappend --port 10001 --replSet hw/localhost:10002  --master

	//启动节点2：
	mongod --dbpath D:\mongodb\dbs\node2 --logpath D:\mongodb\logs\node2\logs.txt --logappend --port 10002 --replSet hw/localhost:10001

	//启动节点3：  
	mongod --dbpath D:\mongodb\dbs\node3 --logpath D:\mongodb\logs\node3\logs.txt --logappend --port 10003 --replSet hw/localhost:10001,localhost:10002

	//初始化节点(只能初始化一次)：随便登录一个节点,以10001为例
	mongo localhost:10001/admin
	db.runCommand({  "replSetInitiate":{   
		"_id":hw",   
		"members":[    
			{      
				"_id":1,     
				"host":"localhost:10001",       
				"priority":3    
			},    
			{      
				"_id":2,      
				"host":"localhost:10002",      
				"priority":2    
			},    
			{      
				"_id":3,      
				"host":"localhost:10003",       
				"priority":1   
			 }   
		]
	}});

查询当前主库，登录10002
	
	mongo localhost:10002
	db.$cmd.findOne ( {ismaster: 1 } ); 

关闭10001服务Dos命令窗口,  登录10002查询当前主库
	
	mongo localhost:10002
	db.$cmd.findOne ( {ismaster: 1 } ); 

