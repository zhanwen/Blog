---
title: Sqoop-1.4.4工具import和export使用详解
date: 2017-12-12 23:33:55
tags:
	- 大数据
	- Sqoop
---

Sqoop-1.4.4工具import和export使用详解
Sqoop可以在HDFS/Hive和关系型数据库之间进行数据的导入导出，其中主要使用了import和export这两个工具。这两个工具非常强大，提供了很多选项帮助我们完成数据的迁移和同步。比如，下面两个潜在的需求：
业务数据存放在关系数据库中，如果数据量达到一定规模后需要对其进行分析或同统计，单纯使用关系数据库可能会成为瓶颈，这时可以将数据从业务数据库数据导入（import）到Hadoop平台进行离线分析。
对大规模的数据在Hadoop平台上进行分析以后，可能需要将结果同步到关系数据库中作为业务的辅助数据，这时候需要将Hadoop平台分析后的数据导出（export）到关系数据库。
这里，我们介绍Sqoop完成上述基本应用场景所使用的import和export工具，通过一些简单的例子来说明这两个工具是如何做到的。
工具通用选项
import和export工具有些通用的选项，如下表所示：

数据导入工具import
import工具，是将HDFS平台外部的结构化存储系统中的数据导入到Hadoop平台，便于后续分析。我们先看一下import工具的基本选项及其含义，如下表所示：

下面，我们通过实例来说明，在实际中如何使用这些选项。
将MySQL数据库中整个表数据导入到Hive表

将MySQL数据库workflow中project表的数据导入到Hive表中。
将MySQL数据库中多表JION后的数据导入到HDFS
<!-- more -->
这里，使用了--query选项，不能同时与--table选项使用。而且，变量$CONDITIONS必须在WHERE语句之后，供Sqoop进程运行命令过程中使用。上面的--target-dir指向的其实就是Hive表存储的数据目录。
将MySQL数据库中某个表的数据增量同步到Hive表

这里，每次运行增量导入到Hive表之前，都要修改--last-value的值，否则Hive表中会出现重复记录。
将MySQL数据库中某个表的几个字段的数据导入到Hive表

我们这里将MySQL数据库workflow中tags表的id和tag字段的值导入到Hive表tag_db.tags。其中--create-hive-table选项会自动创建Hive表，--hive-import选项会将选择的指定列的数据导入到Hive表。如果在Hive中通过SHOW TABLES无法看到导入的表，可以在conf/hive-site.xml中显式修改如下配置选项：


