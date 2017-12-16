---
title: sqoop-export
date: 2017-12-16 19:27:08
tags:
	- Sqoop
	- 大数据
---

# 数据导出工具export

export工具，是将HDFS平台的数据，导出到外部的结构化存储系统中，可能会为一些应用系统提供数据支持。我们看一下export工具的基本选项及其含义，如下表所示

| 选项 |含义说明|
| - | :-: | -: |  
| --validate &lt;class-name&gt;|启用数据副本验证功能，仅支持单表拷贝，可以指定验证使用的实现类 | 
| --validation-threshold &lt;class-name&gt; | 指定验证门限所使用的类 | 
| --direct | 使用直接导出模式（优化速度）|
| --export-dir &lt;dir&gt; | 导出过程中HDFS源路径|
| -m,--num-mappers &lt;n&gt; |使用n个map任务并行导出| 
| --table &lt;table-name&gt;|导出的目的表名称 | 
| --call &lt;stored-proc-name&gt; | 导出数据调用的指定存储过程名 | 
| --update-key &lt;col-name&gt; | 更新参考的列名称，多个列名使用逗号分隔|
| --update-mode &lt;mode&gt; | 指定更新策略，包括：updateonly（默认）、allowinsert|
| --input-null-string &lt;null-string&gt; |使用指定字符串，替换字符串类型值为null的列|
| --input-null-non-string &lt;null-string&gt; | 使用指定字符串，替换非字符串类型值为null的列|
| --staging-table &lt;staging-table-name&gt; | 在数据导出到数据库之前，数据临时存放的表名称|
| --clear-staging-table | 清除工作区中临时存放的数据|
| --batch | 使用批量模式导出|

下面，我们通过实例来说明在实际中如何使用这些选项。这里我们主要结合一个实例，讲解如何将Hive中的数据导入到MySQL数据库。 首先，我们准备几个表，MySQL数据库为tag_db，里面有两个表，定义如下所示：

	CREATE TABLE tag_db.users ( 
		id INT(11) NOT NULL AUTO_INCREMENT,name VARCHAR(100) NOT NULL,PRIMARY KEY (`id`) 
		) ENGINE=InnoDB DEFAULT CHARSET=utf8; 
	CREATE TABLE tag_db.tags ( 
		id INT(11) NOT NULL AUTO_INCREMENT, 
		user_id INT NOT NULL, 
		tag VARCHAR(100) NOT NULL, 
		PRIMARY KEY (`id`)) ENGINE=InnoDB DEFAULT CHARSET=utf8;

这两个表中存储的是基础数据，同时对应着Hive中如下两个表：

	CREATE TABLE users (id INT,name STRING); 
	CREATE TABLE tags (id INT,user_id INT,tag STRING);
我们首先在上述MySQL的两个表中插入一些测试数据

	INSERT INTO tag_db.users(name) VALUES('jeffery');
	INSERT INTO tag_db.users(name) VALUES('shirdrn');
	INSERT INTO tag_db.users(name) VALUES('sulee');
	INSERT INTO tag_db.tags(user_id, tag) VALUES(1, 'Music');
	INSERT INTO tag_db.tags(user_id, tag) VALUES(1, 'Programming');
	INSERT INTO tag_db.tags(user_id, tag) VALUES(2, 'Travel');
	INSERT INTO tag_db.tags(user_id, tag) VALUES(3, 'Sport');
然后，使用Sqoop的import工具，将MySQL两个表中的数据导入到Hive表，执行如下命令行：

	bin/sqoop import --connect jdbc:mysql://10.95.3.49:3306/tag_db --table users --username shirdrn -P --hive-import -- --default-character-set=utf-8
	bin/sqoop import --connect jdbc:mysql://10.95.3.49:3306/tag_db --table tags --username shirdrn -P --hive-import -- --default-character-set=utf-8
导入成功以后，再在Hive中创建一个用来存储users和tags关联后数据的表：

	CREATE TABLE user_tags (
		id STRING,
		name STRING,
		tag STRING
		);
执行如下HQL语句，将关联数据插入user_tags表：

	FROM users u JOIN tags t ON u.id=t.user_id INSERT INTO TABLE user_tags SELECT CONCAT(CAST(u.id AS STRING),CAST(t.id AS STRING)), u.name, t.tag;
将users.id与tags.id拼接的字符串，作为新表的唯一字段id，name是用户名，tag是标签名称。 再在MySQL中创建一个对应的user_tags表，如下所示：

	CREATE TABLE tag_db.user_tags (
		id varchar(200) NOT NULL,
		name varchar(100) NOT NULL,
		tag varchar(100) NOT NULL
		);
使用Sqoop的export工具，将Hive表user_tags的数据同步到MySQL表tag_db.user_tags中，执行如下命令行：

	bin/sqoop export --connect jdbc:mysql://10.95.3.49:3306/tag_db --username shirdrn --P --table user_tags --export-dir /hive/user_tags --input-fields-terminated-by '\001' -- --default-character-set=utf-8