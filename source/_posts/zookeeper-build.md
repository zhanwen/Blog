---
title: zookeeper环境搭建
date: 2017-10-21 22:33:37
tags:
	- Zookeeper
---

1.下载zookeeper压缩包，若是在虚拟机上跑的机器，需要将zk上传，也可以通过yum命令直接在虚拟机里面下载。

2.解压
		
	tar -zxvf zookeeper压缩包

<!-- more -->

3.配置（先在一台节点上配置）
&nbsp;&nbsp;&nbsp;&nbsp;3.1 添加一个zoo.cfg配置文件,该配置文件在Zookeeper根目录/conf下面

	mv zoo_sample.cfg zoo.cfg
	
&nbsp;&nbsp;&nbsp;&nbsp;3.2 修改配置文件（zoo.cfg）

		dataDir=/home/tools/hadoop/zookeeper-3.4.5/data
		server.1=ip:2888:3888
		server.2=ip:2888:3888
		server.3=ip:2888:3888
	
&nbsp;&nbsp;&nbsp;&nbsp;3.3 在（dataDir=/home/tools/hadoop/zookeeper-3.4.5data）创建一个myid文件，里面内容是server.N
中的N（server.2里面内容为2）

		echo "1" > myid
	
&nbsp;&nbsp;&nbsp;&nbsp;3.4将配置好的zk拷贝到其他节点
	
		scp -r /yun/zookeeper-3.4.5/ yun2:/tools/
		scp -r /yun/zookeeper-3.4.5/ yun3:/tools/
	
&nbsp;&nbsp;&nbsp;&nbsp;3.5注意：在其他节点上一定要修改myid的内容
		
		在yun2应该讲myid的内容改为2 （echo "2" > myid）
		在yun3应该讲myid的内容改为3 （echo "3" > myid）
		
4.启动集群
&nbsp;&nbsp;&nbsp;&nbsp;分别启动zk
		
	./zkServer.sh start
	
# zookeeper的最主要功能：
1、保管客户端提交的数据（极其少量的数据),每一份数据在zookeeper叫做一个znode,znode之间形成一种树状结构（类似于文件树）
比如：

	/lock(znode名) host001(znode中存的数据)
	/lock/last_acc(znode名)    host008(znode中存的数据)

