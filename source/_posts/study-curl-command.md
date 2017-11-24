---
title: 学会使用curl命令
date: 2017-11-24 22:54:39
tags:
	- Linux
---

curl是linux系统命令行下用来简单测试web访问的工具，几个常用的选项你要掌握

	curl -xip:port www.baidu.com # -x可以指定ip和端口，省略写hosts，方便实用

	curl -Iv http://www.qq.com #-I可以把访问的内容略掉，只显示状态码，-v可以显示详细过程

	curl -u user：password http://123.com #-u可以指定用户名和密码

	curl http://study.lishiming.net/index.html -O #可以下载，还可以使用-o自定义名字
	
	curl -o index2.html http://study.lishiming.net/index.html