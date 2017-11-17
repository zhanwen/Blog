---
title: 一些shell中常用的命令cut
date: 2017-11-17 22:09:02
tags:
	- Shell
	- Linux
---


语法
	
	cut -d ‘分割字符’【-cf】 n 这里的n是正整数
	
	-d后面指定分隔符，用单引号引起来，-f指定第几段
	cut -d ‘：‘  -f 1 /etc/passwd | head -n 5

	-c后面只有一个数字表示截取第几个字符
	head -n2 /etc/passwd | cut -c2

	-f 后面跟一个数字区域，表示截取从几到几
	head -n2 /etc/passwd | cut -c2-5
<!-- more -->
SORT语法
	
	sort【-t分隔符】【-kn1，n2】【-nru】 （n1<n2)

	不加选项，从首字符向后，依次按ASCII码值进行升序排序
	sort /etc/passwd

	-t后指定分隔符，-kn1，n2表示在指定的区间中排序，-k后面只跟一个数字表示对第n个字符排序，-n表示使用纯数字排序
	sort -t： -k3 -n /etc/passwd

	-r 表示以降序的形式排序 
	sort -t: -k3,5 -r /etc/passwd

	-u 去重
	cut -d: -f4 /etc/passwd |sort -n u

WC用于统计文档的行数、字符数、词数

	不加任何选项，会显示行数，词数以及字符数
	-l统计行数
	-m统计字符数
	-w统计字数

	-uniq，tee，tr
	uniq去重复，最常用就一个-c用来统计重复的行数，去重前要先排序
	sort tsetb.txt |uniq -c

	tee后跟文件名，类似于>，比重定向多了一个功能，在把文件写入后面所跟的文件中的同时，还显示在屏幕上

	tr替换字符，最常用的就是大小写转换：
	head -n2 /etc/passwd |tr '[a-z]' '[A-Z]'

	tr替换一个字符也是可以的
	grep 'root' /etc/passwd | tr 'r' 'R'

Split切割大文件用

	-b：按大小来分割单位为byte
	split -b50 1.txt

	默认会以xaa，xab，。。。这样的形式定义分割后的文件名，也可以指定文件名
	split -b50 1.txt 123

	-l：按行数分割，split -l10 file


















