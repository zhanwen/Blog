---
title: mysql备份和恢复
date: 2017-11-09 22:45:18
tags:
	- Mysql
	- 数据库=
---

备份
	
	mysqldump -uroot -p db > 1.sql

恢复
	
	mysql -uroot -p db < 1.sql

只备份一个表
	
	mysqldumpp -uroot -p db tb1 > 2.sql

备份时指定字符集
	
	mysqldump -uroot -p --default-character-set=utf8 db >1.sql

恢复也指定字符集
	
	mysql -uroot -p --default-character=utf8 db < 1.sql

这是myisam和innodb都可以用上述的方式备份，mysqldump。
myisam引擎特点：可以直接拷贝源文件，比较小
innodb引擎不可以，因为里面是个很大的文件，虚拟的内存，比较大。
