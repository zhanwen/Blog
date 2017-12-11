---
title: linux下的日期时间
date: 2017-12-11 22:54:23
tags:
	- Linux
---
用`date`更新时间与本机一致,使用`ntpdate`这个命令  
	
	先安装
	yum install -y ntp
	然后执行就可以了
	ntpdate time.windows.com

打印六位的年月日
	
	date +%y%m%d
	150904
<!-- more -->
打印八位的年月日

	date +%Y%m%d
	20150904

还可以指定格式
	
	date +%Y-%m-%d

指定明天或者是后天，加上-d
	
	date -d "+1day" +%Y%m%d

或者一小时
	
	date -d "-1hour" +%H:%M:%S

或者一分钟
	
	date -d "-1min" +%H:%M:%S

一周之前

	date -d "-1week" +%Y%m%d

时间
	
	date +%H:%M:%S = date +%T
	date +%s 时间戳

	date +%w, date +%W星期

一月后
	
	date -d "1month"