---
title: hive-hql详解
date: 2017-10-29 09:43:06
tags:	
	- Hive 
	- 大数据
---

	set hive.cli.print.header=true;

	CREATE TABLE page_view(viewTime INT, userid BIGINT,
	     page_url STRING, referrer_url STRING,
	     ip STRING COMMENT 'IP Address of the User')
	 COMMENT 'This is the page view table'
	 PARTITIONED BY(dt STRING, country STRING)
	 ROW FORMAT DELIMITED
	   FIELDS TERMINATED BY '\001'
	STORED AS SEQUENCEFILE;   TEXTFILE
<!-- more -->
sequencefile
	
	create table tab_ip_seq(id int,name string,ip string,country string) 
	    row format delimited
	    fields terminated by ','
	    stored as sequencefile;
	insert overwrite table tab_ip_seq select * from tab_ext;


create & load

	create table tab_ip(id int,name string,ip string,country string) 
	    row format delimited
	    fields terminated by ','
	    stored as textfile;
	load data local inpath '/home/hadoop/ip.txt' into table tab_ext;

external,外部表被drop时，只清除元数据，表数据并不会被删除
	
	CREATE EXTERNAL TABLE tab_ip_ext(id int, name string,
	     ip STRING,
	     country STRING)
	 ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
	 STORED AS TEXTFILE
	 LOCATION '/external/hive';
 

CTAS  用于创建一些临时表存储中间结果
	
	CREATE TABLE tab_ip_ctas
	   AS
	SELECT id new_id, name new_name, ip new_ip,country new_country
	FROM tab_ip_ext
	SORT BY new_id;


insert from select   用于向临时表中追加中间结果数据
	
	create table tab_ip_like like tab_ip;

	insert overwrite table tab_ip_like
	    select * from tab_ip;

CLUSTER <--相对高级一点，你可以放在有精力的时候才去学习>
	
	create table tab_ip_cluster(id int,name string,ip string,country string)
	clustered by(id) into 3 buckets;

	load data local inpath '/home/hadoop/ip.txt' overwrite into table tab_ip_cluster;
	set hive.enforce.bucketing=true;
	insert into table tab_ip_cluster select * from tab_ip;

	select * from tab_ip_cluster tablesample(bucket 2 out of 3 on id); 

PARTITION
	
	create table tab_ip_part(id int,name string,ip string,country string) 
	    partitioned by (part_flag string)
	    row format delimited fields terminated by ',';
	    
	load data local inpath '/home/hadoop/ip.txt' overwrite into table tab_ip_part
	     partition(part_flag='part1');
	    
	    
	load data local inpath '/home/hadoop/ip_part2.txt' overwrite into table tab_ip_part
	     partition(part_flag='part2');

	select * from tab_ip_part;

	select * from tab_ip_part  where part_flag='part2';
	select count(*) from tab_ip_part  where part_flag='part2';

	alter table tab_ip change id id_alter string;
	ALTER TABLE tab_cts ADD PARTITION (partCol = 'dt') location '/external/hive/dt';

	show partitions tab_ip_part;
   
write to hdfs
	
	insert overwrite local directory '/home/hadoop/hivetemp/test.txt' select * from tab_ip_part where part_flag='part1';    
	insert overwrite directory '/hiveout.txt' select * from tab_ip_part where part_flag='part1';
	(不支持into，也就是不支持文件的追加)

array 
	
	create table tab_array(a array<int>,b array<string>)
	row format delimited
	fields terminated by '\t'
	collection items terminated by ',';

示例数据
	
	tobenbrone,laihama,woshishui     13866987898,13287654321
	abc,iloveyou,itcast     13866987898,13287654321

	select a[0] from tab_array;
	select * from tab_array where array_contains(b,'word');
	insert into table tab_array select array(0),array(name,ip) from tab_ext t; 

map
	
	create table tab_map(name string,info map<string,string>)
	row format delimited
	fields terminated by '\t'
	collection items terminated by ';'
	map keys terminated by ':';

示例数据：
	
	fengjie			age:18;size:36A;addr:usa
	furong	    age:28;size:39C;addr:beijing;weight:90KG

	load data local inpath '/home/hadoop/hivetemp/tab_map.txt' overwrite into table tab_map;
	insert into table tab_map select name,map('name',name,'ip',ip) from tab_ext; 

struct
	
	create table tab_struct(name string,info struct<age:int,tel:string,addr:string>)
	row format delimited
	fields terminated by '\t'
	collection items terminated by ','

	load data local inpath '/home/hadoop/hivetemp/tab_st.txt' overwrite into table tab_struct;
	insert into table tab_struct select name,named_struct('age',id,'tel',name,'addr',country) from tab_ext;

cli shell
	
	[hadoop@yun~]hive -S -e 'select country,count(*) from tab_ext' > /home/hadoop/hivetemp/e.txt  
	有了这种执行机制，就使得我们可以利用脚本语言（bash shell,python）进行hql语句的批量执行

	select * from tab_ext sort by id desc limit 5;

	select a.ip,b.book from tab_ext a join tab_ip_book b on(a.name=b.name);

UDF
	
	select if(id=1,first,no-first),name from tab_ext;

	hive>add jar /home/hadoop/myudf.jar;
	hive>CREATE TEMPORARY FUNCTION my_lower AS 'org.dht.Lower';
	select my_upper(name) from tab_ext;  



