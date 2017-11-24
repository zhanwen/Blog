---
title: rsync应用实例
date: 2017-11-24 22:43:24
tags:
	- Linux
---
ssh方式

	1、rsync -avL test1/ www@192.168.238.0.101:/tmp/test2/

	2、rsync -avL 192.168..0.101:/tmp/test2/ ./test3/

3、由于需要输入密码所以不合适写到脚本中，但可以通过创建密钥对，让两台机器产生信任关系从而不用输入密码
	
	ssh-keygen -t rsa
	连续回车
	ssh-copy-id id_rsa.pub hostip

<!-- more -->
4、如果ssh端口不是22，那么需要写成下面这样的形式：
	
	rsync -av "--rsh=ssh -p [port]  " /dir1/192.168.0.101:/tmp/dir2/

后台服务方式, 配置文件/etc/rsyncd.conf，内容如下：
	
	#port=873 #监听端口默认为873，也可以是别的端口
	log file=/var/log/rsync.log #指定日志
	pid file=/var/run/rsyncd.pid #指定pid
	#address=192.168.238.2 #可以定义绑定的ip

以上部分为全局配置部分，以下为模块内的设备

	[test] #为模块名，自定义
	path=/root/rsync #指定该模块对应在哪个目录下
	use chroot=true #是否限定在该目录下，默认为true，当有软链接时，需要改为false
	max connections=4 #指定最大可以连接的客户端数
	read only=no #是否为只读
	list=true #是否可以列出模块名
	uid=root #以哪个用户的身份来传输
	gid=root #以哪个组的身份来传输
	auth users=test #指定验证用户名，可以不设置
	secrets file=/etc/rsyncd.passwd #指定密码文件，如果设定验证用户，这一项必须设置
	hosts allow=192.168.238.22 #设置可以允许访问的主机，可以是网段

密码文件/etc/rsyncd.passwd的内容格式为

	username：password

启动服务的命令是
	
	rsync --daemon

默认去使用/etc/rsyncd.conf 这个配置文件，也可以指定配置文件

	rsync --daemon--config=/etc/rsync2.conf

可使用的选项有
	
	rsync --daemon -help

几个测试点：port，use，chroot，log file，secrets file，hosts，allow，list。













