---
title: mac上安装mysql笔记
date: 2017-10-20 08:43:51
tags:	
	- Mysql
	- Linux
---

1、从mysql官网上找到download，然后选择下面的Mysql Community Server，找到对应的mac版本，即后缀名为.dmg的文件，点击下载,然后点击下图中的按钮即可下载。
![Alt text](/images/mysql-install.jpg)

2、下载之后打开对应的dmg文件，会弹出安装提示框，按照提示框提示一直点击下一步，等待安装完成。安装完成之后会出现一个对话框，提示你这是mysql的root账户的初始密码，在退出该对话框之前，请先保存！！！
<!-- more -->
3、打开终端，输入下面命令
	
	mysql -uroot -p 

会提示错误，找不到mysql命令，这是我们需要配置一下我们的环境变量，编辑/etc/profile文件
	
	vi /etc/profile

发现没有权限，是因为我们是用自己的账户进入的，也就是你当前的账户不是root账户，需要我们切换到root账户下，然后在去编辑该文件。对于没有使用过root账户的，这是我们执行下面的命令

	su root

会提示我们输入密码，然后输入root账户的密码，但是我们之前一直没有使用过root密码，所以这时候我们需要重新设置一下root密码，然后在重新进入root账户下即可。执行以下命令
	
	su passwd root

提示输入新的密码，这时我们就可以重新设置密码了。设置密码之后，再切换到root账户下。

	su root

输入我们刚才设置的密码，就可以切换到root账户下。这时我们编辑/etc/profile文件，将mysql添加到path目录下。

	PATH=$PATH:/usr/local/mysql/bin
	//保存退出的时候，如果发现没有权限对该文件写操作，可以先使用 `q!` 命令强制退出。
	//然后使用chmod重新授权
	chmod -R 755 /etc/profile

保存退出之后，刷新一下该文件

	source /etc/profile

这时我们在进入mysql命令行界面。输入以下命令

	mysql -uroot -p

输入我们一开始安装完成mysql时弹出的初始密码，就可以进入mysql命令行界面。进入命令行之后，我们执行下面的命令。

	show databases;

却发现提示错误，提示我们在执行命令之前要先更新密码，然后在执行命令。这时我们要更新密码。更新密码之前，我们先打开系统偏好设置，在最下面找到mysql，将mysql停止掉。然后我们在命令行下面输入下面的命令

	sudo /usr/local/mysql/bin/mysqld_safe --skip-grant-tables &

‘&’ 符号的作用时在后台执行。然后使用updata语句更新我们的密码。

	UPDATE mysql.user SET authentication_string=PASSWORD('new password') WHERE User='root';

刷新

	FLUSH PRIVILEGES;

最后重启我们的mysql服务，再次进入mysql命令行界面，重新执行语句就OK了。但是为了安全起见，我们要建立一个当前mysql帐号，不要使用mysql的root账户进入。我们先进入mysql命令行下面，然后执行下面的命令：

	create user hanwen identified by '123456';

这样我们就建立一个新的mysql账户，也可以授权给该用户。
	
	create database test; //创建test数据库
	//该用户拥有test数据库的所有权限
	grant all privileges on test.* to hanwen@localhost identified by '123456';
	//或者授予root权限
	GRANT ALL PRIVILEGES ON *.* TO 'hanwen'@'%';
	flush privileges;//刷新系统权限表

我们重新退出，在使用新用户登录即可。



