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

<!-- more -->
更详细的请点击[这里](https://github.com/zhanwen/Blog/blob/master/other/sqoop-export.md)