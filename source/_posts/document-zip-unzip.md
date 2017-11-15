---
title: Linux文档的压缩与打包
date: 2017-11-15 22:04:40
tags:
	- Linux
---
可以打包目录也可以打包文件，语法
	
	tar [-zjxcvfpP] filename

	//其中test是文件或目录
	tar -cvf test.tar test 
	
	-c表示建立包，-v可视化，压缩时跟“-f文件名”，意思是压缩后的文件名为filename，解压时跟“-f文件名”，意思是解filename。请注意，如果是多个参数组合的情况下带有“-f”，请把“-f”写到最后面


查看包内容
	
	//-t：查看tar包里面的文件
	tar -tf test.tar

<!-- more -->
解包
	
	//-x：解包或者解压缩	
	tar -xvf test.tar
不管是打包还是解包，原来的文件是不会删除的，但它会覆盖当前已经存在的文件或者目录。

打包的同时使用gzip压缩

	//-z表示打包同时使用gzip压缩
	tar -czvf 1.tar.gz 1 //其中1可以是文件也可以是目录

解压.tar.gz的压缩包
	
	tar -xzvf 1.tar.gz

使用bzip2压缩

	-j表示打包同时使用bzip2压缩	
	tar -jzvf 1.tar.bz2 1

解压.tar.bz2
	
	tar -xjvf 1.tar.bz2

同样使用tar -tf 查看压缩的包

	tar -tf 1.tar.gz 或者 tar -tf 1.tar.bz2

--exclude可以在打包的时候，排除某些文件或者目录
	
	tar -cvzf 1.tar.gz --exclude 1.txt dir/

排除多个文件或者目录

	tar -czvf 1.tar.gz --exclude 1.txt --exclude 123/ dir/

# zip和unzip
zip是压缩工具，unzip是解压缩工具
压缩文件
	
	zip filename.zip filename

压缩目录 
	
	zip -r dir.zip dir/

解压缩zip压缩包
	
	unzip filename.zip

# gzip压缩，语法

	gzip [-d#] filename 其中#为1-9的数字，默认压缩级别为6

只能压缩文件，不能压缩目录

	gzip filename 生成filename.gz 源文件消失

解压
	
	gzip -d filename.gz 解压后，压缩文件也会消失

# bzip2压缩工具
语法
	
	bzip2 [-dz] filename //“-z”表示压缩，也可以不加

压缩时，可以加“-z”也可以不加，都可以压缩文件 
	
	bzip2 filename 生成filename.bz2。源文件消失

不支持压缩目录，只支持压缩文件

	bzip2 -d filename.bz2   	//解压后压缩文件消失

可以使用bzcat查看bz2的压缩前的文件内容





