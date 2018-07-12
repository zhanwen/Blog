---
title: DataNode节点超时时间设置
date: 2017-10-18 23:30:50
tags: 
	- Hadoop
	- 大数据

---

DataNode 进程死亡或者网络故障造成 DataNode 无法与 NameNode 通信，NameNode 不会立即把该节点判定为死亡，要经过一段时间，这段时间暂称作超时时长。HDFS 默认的超时时长为 10 分钟 + 30 秒。如果定义超时时间为 timeout，则超时时长的计算公式为：

    timeout  = 2 * heartbeat.recheck.interval + 10 * dfs.heartbeat.interval。
<!-- more -->

而默认的 heartbeat.recheck.interval 大小为 5 分钟，dfs.heartbeat.interval 默认为 3 秒。需要注意的是 hdfs-site.xml 配置文件中的 heartbeat.recheck.interval 的单位为毫秒， dfs.heartbeat.interval 的单位为秒。所以，举个例子，如果 heartbeat.recheck.interval 设置为 5000（毫秒），dfs.heartbeat.interval 设置为 3（秒，默认），则总的超时时间为 40 秒。
	

hdfs-site.xml 中的参数设置格式：

	<property>
        <name>heartbeat.recheck.interval</name>
        <value>2000</value>
	</property>
	<property>
        <name>dfs.heartbeat.interval</name>
        <value>1</value>
	</property>
