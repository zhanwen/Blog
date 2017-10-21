---
title: zookeeper配置文件详解
date: 2017-10-21 22:22:52
tags: 
	- Zookeeper
---

zookeeper的默认配置文件为zookeeper/conf/zoo_sample.cfg，需要将其修改为zoo.cfg。在修改之前，最好保留一份。

	cp zoo_sample.cfg zoo.cfg

其中各配置项的含义，解释如下：

<!-- more -->

1.tickTime：CS通信心跳时间
Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳。tickTime以毫秒为单位。
 
	tickTime=2000  

2.initLimit：LF初始通信时限
集群中的follower服务器(F)与leader服务器(L)之间初始连接时能容忍的最多心跳数（tickTime的数量）。
	
	initLimit=5  

3.syncLimit：LF同步通信时限
集群中的follower服务器与leader服务器之间请求和应答之间能容忍的最多心跳数（tickTime的数量）。

	syncLimit=2  
 
4.dataDir：数据文件目录
Zookeeper保存数据的目录，默认情况下，Zookeeper将写数据的日志文件也保存在这个目录里。

	dataDir=/home/tools/opt/zookeeper/data  

5.clientPort：客户端连接端口
客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。
	
	clientPort=2181 

6.服务器名称与地址：集群信息（服务器编号，服务器地址，LF通信端口，选举端口）这个配置项的书写格式比较特殊，规则如下：
2888端口号是zookeeper服务之间通信的端口，而3888是zookeeper与其他应用程序通信的端口

	server.N=ip:A:B 
	server.1=ip:2888:3888
	server.2=ip:2888:3888
	server.3=ip:2888:3888




