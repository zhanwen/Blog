---
title: hbase-集群
date: 2017-10-23 23:05:50
tags:
	- Hbase
	- 大数据
---

1.上传hbase安装包

2.解压
	
	tar -zxvf hbase.xx.tar.gz

3.配置hbase集群，要修改3个文件（首先zk集群已经安装好了）
	注意：要把hadoop的hdfs-site.xml和core-site.xml 放到hbase/conf下
	
<!-- more -->

	3.1 修改hbase-env.sh
	export JAVA_HOME=/usr/java/jdk1.7.0_55
	//告诉hbase使用外部的zk
	export HBASE_MANAGES_ZK=false
	
	vim hbase-site.xml
	<configuration>
		<!-- 指定hbase在HDFS上存储的路径 -->
        <property>
                <name>hbase.rootdir</name>
                <value>hdfs://ns1/hbase</value>
        </property>
		<!-- 指定hbase是分布式的 -->
        <property>
                <name>hbase.cluster.distributed</name>
                <value>true</value>
        </property>
		<!-- 指定zk的地址，多个用“,”分割 -->
        <property>
                <name>hbase.zookeeper.quorum</name>
                <value>weekend05:2181,weekend06:2181,weekend07:2181</value>
        </property>
	</configuration>
	
	vim regionservers
	yun3
	yun4
	yun5
	yun6
	
	3.2拷贝hbase到其他节点
		scp -r /yun/hbase-0.96.2-hadoop2/ yun2:/app/
		scp -r /yun/hbase-0.96.2-hadoop2/ yun3:/app/
		scp -r /yun/hbase-0.96.2-hadoop2/ yun4:/app/
		scp -r /yun/hbase-0.96.2-hadoop2/ yun5:/app/
		scp -r /yun/hbase-0.96.2-hadoop2/ yun6:/app/

4.将配置好的HBase拷贝到每一个节点并同步时间。

5.启动所有的hbase

	分别启动zk
		./zkServer.sh start
	启动hdfs集群
		start-dfs.sh
	启动hbase，在主节点上运行：
		start-hbase.sh

6.通过浏览器访问hbase管理页面
	192.168.31.123:60010

7.为保证集群的可靠性，要启动多个HMaster
	hbase-daemon.sh start master
	
	

	
	

