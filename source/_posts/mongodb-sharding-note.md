---
title: mongodb分布式存储
date: 2017-11-13 22:29:57
tags:
	- MongoDB
	- 数据库
---

分片(sharding)是指将数据拆分，将其分散存在不同的机器上的过程。有时也用分区(partitioning)来表示这个概念。将数据分散到不同的机器上，不需要功能强大的大型计算机就可以储存更多的数据，处理更多的负载。

MongoDB分片的基本思想就是将集合切分成小块。这些块分散到若干片里面，每个片只负责总数据的一部分。应用程序不必知道哪片对应哪些数据，甚至不需要知道数据已经被拆分了，所以在分片之前要运行一个路由进程，该进程名为mongos。这个路由器知道所有数据的存放位置，所以应用可以连接它来正常发送请求。对应用来说，它仅知道连接了一个普通的mongod。路由器知道数据和片的对应关系，能够转发请求到正确的片上。如果请求有了回应，路由器将其收集起来回送给应用。

设置分片时，需要从集合里面选一个键，用该键的值作为数据拆分的依据。这个键称为片键(shard key)。
	
	{name:"zhangsan",age:1}

用个例子来说明这个过程：假设有个文档集合表示的是人员。如果选择名字("name")作为片键，第一片可能会存放名字以A~F开头的文档，第二片存的G~P的名字，第三片存的Q~Z的名字。随着添加或者删除片，MongoDB会重新平衡数据，使每片的流量都比较均衡，数据量也在合理范围内。
<!-- more -->
分片原理
![Alt text](/images/sharding.jpg)

实战操作
	
	#1、创建三个目录，分别存放两个mongod服务的数据文件和config服务的数据文件
	mongod_node1
	mongod_node2
	config_node

	#2、开启config服务器 。mongos要把mongod之间的配置放到config服务器里面，所以首先开启它，这里就使用2222端口。 命令为：
	mongod --dbpath \config_node --port 2222

	#3、开启mongos服务器 。这里要注意的是我们开启的是mongos，端口3333，同时指定下config服务器。命令为：
	mongos --port 3333 --configdb=127.0.0.1:2222

	#4、启动mongod服务器 。对分片来说，也就是要添加片了，这里开启两个mongod服务，端口分别为：4444，5555。命令为：
	mongod --dbpath \mongod_node1 --port 4444
	mongod --dbpath \mongod_node2 --port 5555 
	
	#5、服务配置 。client直接跟mongos打交道，也就说明我们要连接mongos服务器，然后将4444，5555的mongod交给mongos,添加分片也就是addshard()。
	db.runCommand({"addshard":127.0.0.1:4444", allowLocal:true})
	db.runCommand({"addshard":127.0.0.1:5555", allowLocal:true})
	
	#6、开启数据库分片功能，命令很简单 enablesharding(), 这里就开启test数据库。 
	db.runCommand({"enablesharding":"test"})	//test为数据库
	
	#7、指定集合中分片的片键，这里就指定为person.name键。 
	db.runCommand({shardcollection":"test.person", "key":{"name":1}})

	#8、通过mongos插入100w记录，然后通过printShardingStatus命令查看mongodb的数据分片情况。
	```
	for(var i = 0; i < 1000000; i++) {
		db.person.insert({"name":"zhang"+i, "age":i})
	}
	db.printShardingStatus()
	```





