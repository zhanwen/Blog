---
title: Hbase配置文件hbase-site.xml
date: 2017-10-28 19:28:51
tags:
	- Hbase
---

	<property>
	        <name>hbase.rootdir</name>
	        <value>hdfs://ns1/hbase</value>
	</property>
	<property>
	        <name>hbase.cluster.distributed</name>
	        <value>true</value>
	</property>
	<property>
	        <name>hbase.zookeeper.quorum</name>
	        <value>yun4:2181,yun5:2181,yun6:2181</value>
	</property>