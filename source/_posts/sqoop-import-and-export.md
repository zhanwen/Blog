---
title: Sqoop-1.4.4工具import和export使用详解
date: 2017-12-12 23:33:55
tags:
	- 大数据
	- Sqoop
---

#### Sqoop-1.4.4工具import和export使用详解

Sqoop可以在HDFS/Hive和关系型数据库之间进行数据的导入导出，其中主要使用了import和export这两个工具。这两个工具非常强大，提供了很多选项帮助我们完成数据的迁移和同步。比如，下面两个潜在的需求：  

1. 业务数据存放在关系数据库中，如果数据量达到一定规模后需要对其进行分析或同统计，单纯使用关系数据库可能会成为瓶颈，这时可以将数据从业务数据库数据导入（import）到Hadoop平台进行离线分析。  

2. 对大规模的数据在Hadoop平台上进行分析以后，可能需要将结果同步到关系数据库中作为业务的辅助数据，这时候需要将Hadoop平台分析后的数据导出（export）到关系数据库。  

这里，我们介绍Sqoop完成上述基本应用场景所使用的import和export工具，通过一些简单的例子来说明这两个工具是如何做到的。
工具通用选项,import和export工具有些通用的选项，如下表所示：

<!-- more -->

| 选项 |含义说明| 
| - | :-: | -: | 
| --connect &lt;jdbc-uri&gt; |指定JDBC连接字符串 | 
| --connection-manager &lt;class-name&gt; | 指定要使用的连接管理器类 | 
| --driver &lt;class-name&gt; | 指定要使用的JDBC驱动类|
| --hadoop-mapred-home &lt;dir&gt; | 指定$HADOOP_MAPRED_HOME路径|
| --help | 打印用法帮助信息|
| --password-file | 设置用于存放认证的密码信息文件的路径|
| -P | 从控制台读取输入的密码|
| --password &lt;password&gt; | 设置认证密码|
| --username &lt;username&gt; | 设置认证用户名|
| --verbose | 打印详细的运行信息|
| --connection-param-file &lt;filename&gt; | 可选，指定存储数据库连接参数的属性文件|

数据导入工具import
import工具，是将HDFS平台外部的结构化存储系统中的数据导入到Hadoop平台，便于后续分析。我们先看一下import工具的基本选项及其含义，如下表所示：

| 选项 |含义说明| 
| - | :-: | -: | 
| --append|将数据追加到HDFS上一个已存在的数据集上 | 
| --as-avrodatafile | 将数据导入到Avro数据文件 | 
| --as-sequencefile | 将数据导入到SequenceFile|
| --as-textfile | 将数据导入到普通文本文件（默认）|
| --boundary-query &lt;statement&gt; | 边界查询，用于创建分片（InputSplit）|
| --columns &lt;col,col,col…&gt; | 从表中导出指定的一组列的数据|
| --delete-target-dir | 如果指定目录存在，则先删除掉|
| --direct | 使用直接导入模式（优化导入速度）|
| --direct-split-size &lt;n&gt; | 分割输入stream的字节大小（在直接导入模式下）|
| --fetch-size &lt;n&gt; | 从数据库中批量读取记录数|
| --inline-lob-limit &lt;n&gt; | 设置内联的LOB对象的大小|
| -m,--num-mappers &lt;n&gt; | 使用n个map任务并行导入数据|
| -e,--query &lt;statement&gt; | 导入的查询语句|
| --split-by &lt;column-name&gt; | 指定按照哪个列去分割数据|
| --table &lt;table-name&gt; | 导入的源表表名|
| --target-dir &lt;dir&gt; | 导入HDFS的目标路径|
| --warehouse-dir &lt;dir&gt; | HDFS存放表的根路径|
| --where &lt;where clause&gt; | 指定导出时所使用的查询条件|
| -z,--compress | 启用压缩|
| --compression-codec &lt;c&gt;| 指定Hadoop的codec方式（默认gzip）|
| --null-string &lt;null-string&gt; | 如果指定列为字符串类型，使用指定字符串替换值为null的该类列的值|
| --null-non-string &lt;null-string&gt; | 如果指定列为非字符串类型，使用指定字符串替换值为null的该类列的值|


下面，我们通过实例来说明，在实际中如何使用这些选项。
将MySQL数据库中整个表数据导入到Hive表
	
```	
bin/sqoop import --connect jdbc:mysql://master1:3306/workflow --table project --username shirdrn -P --hive-import -- --default-character-set=utf-8
```

将MySQL数据库workflow中project表的数据导入到Hive表中
将MySQL数据库中多表JION后的数据导入到HDFS

```
	bin/sqoop import --connect jdbc:mysql://10.95.3.49:3306/workflow --username shirdrn -P --query 'SELECT users.*, tags.tag FROM users JOIN tags ON (users.id = tags.user_id) WHERE $CONDITIONS' --split-byusers.id --target-dir /hive/tag_db/user_tags -- --default-character-set=utf-8
```

这里，使用了--query选项，不能同时与--table选项使用。而且，变量$CONDITIONS必须在WHERE语句之后，供Sqoop进程运行命令过程中使用。上面的--target-dir指向的其实就是Hive表存储的数据目录。
将MySQL数据库中某个表的数据增量同步到Hive表

```	
	bin/sqoop job --create your-sync-job -- import --connect jdbc:mysql://10.95.3.49:3306/workflow --table project --username shirdrn -P --hive-import --incremental append --check-column id --last-value 1 -- --default-character-set=utf-8
```

这里，每次运行增量导入到Hive表之前，都要修改--last-value的值，否则Hive表中会出现重复记录。
将MySQL数据库中某个表的几个字段的数据导入到Hive表

```
	bin/sqoop import --connect jdbc:mysql://10.95.3.49:3306/workflow --username shirdrn --P --table tags --columns 'id,tag' --create-hive-table -target-dir /hive/tag_db/tags -m 1 --hive-table tags --hive-import -- --default-character-set=utf-8
```

我们这里将MySQL数据库workflow中tags表的id和tag字段的值导入到Hive表tag_db.tags。其中--create-hive-table选项会自动创建Hive表，
--hive-import选项会将选择的指定列的数据导入到Hive表。如果在Hive中通过SHOW TABLES无法看到导入的表，可以在conf/hive-site.xml中显式修改如下配置选项：

```
	&lt;property&gt;
		&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
		&lt;value&gt;jdbc:derby:;databaseName=hive_metastore_db;create=true&lt;/value&gt;
	&lt;/property&gt;
```

然后再重新运行，就能看到了。
使用验证配置选项

```	
	sqoop import --connect jdbc:mysql://db.foo.com/corp --table EMPLOYEES --validate --validator org.apache.sqoop.validation.RowCountValidator --validation-threshold org.apache.sqoop.validation.AbsoluteValidationThreshold --validation-failurehandler org.apache.sqoop.validation.AbortOnFailureHandler
```

上面这个是官方用户手册上给出的用法，我们在实际中还没用过这个，有感兴趣的可以验证尝试一下。
