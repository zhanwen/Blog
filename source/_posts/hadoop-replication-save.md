---
title: Hadoop副本存放策略
date: 2017-10-15 22:47:33
tags: 
	- Hadoop
	- 大数据

---

1、先在客户端所连接的datanode上存放一个副本
2、再在另一个机架上选择一个datanode存放第二个副本
3、最后在本机架上根据负载情况随机挑选一个datanode存放第三个副本

<!-- more -->

# 副本数量的配置优先级

1、服务端hdfs-site.xml中可以配置
2、在客户端指定dfs.replication的值
   	  客户端所指定的值优先级更高!!!