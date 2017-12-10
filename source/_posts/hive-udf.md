---
title: hive UDF 的使用
date: 2017-12-10 20:41:08
tags:
	- 大数据
	- Hive
---

首先要继承`org.apache.hadoop.hive.ql.exec.UDF`类实现 `evaluate`

自定义函数调用过程：

1. 添加jar包（在hive命令行里面执行)  
	
	`hive> add jar /root/NUDF.jar;`

2. 创建临时函数  

	`hive> create temporary function getNation as 'com.hw.hive.udf.NationUDF';`
<!-- more -->
3. 调用   
	
	`hive> select id, name, getNation(nation) from beauty;`

4. 将查询结果保存到HDFS中  

	`hive> create table result row format delimited fields terminated by '\t' as select * from beauty order by id desc;`
	`hive> select id, getAreaName(id) as name from tel_rec;`

	`create table result row format delimited fields terminated by '\t' as select id, getNation(nation) from beauties;`