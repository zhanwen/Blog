---
title: linux运维安全
date: 2017-11-27 23:13:55
tags:
	- Linux
---
	
1. 账号以及密码一定要复杂，密码需要符合这一的规范：字符大于10个；至少包含大小写以及数字；密码中不能包含账号，不能包含自己的姓名全拼，不能有自己的生日数字，不能有自己的电话号码；密码要定期更换；不能把密码保存在记事本等文档中要用专业的存密码的软件保存；

2. 可以拿一台机器作为跳板机来登陆其他服务器，其他服务器做登陆ip限制
	
	/etc/shos.allow, /etc/hosts.deny

3. 能使用密钥尽量避免使用密码登陆     
	
	vim /etc/ssh/sshd_config  //PermitRootLogin without-password 改为 PermitRootLogin no

4. 可以禁止root直接登陆服务器，只允许普通用户登录，普通用户su到root（PermitRootLogin no）    
	
	vim /etc/ssh/sshd_config               
	chkconfig --list                               
	chkconfig nginx off

<!-- more -->

5. 服务器上用不到的端口关闭，用不到的服务停掉（ntsysv）

6. 应用环境程序软件（apache，nginx，php，mysql）避免使用太老版本

7. 不可逆操作在操作前一定要备份相关的数据或配置文件

8. 重要数据一定要备份，尽量本地和远程存两份

9. 敲命令切勿太快，避免误操作。 

10. web禁止目录浏览
	
	（apache：Option -Indexes；nginx编译时加上 --without-http_autoindex_module）

11. web可写目录下禁止解析php
	
	（apache：php_admin_flag engine off；nginx：location ～.*abc/.*\.php?${deny all;}

12. web默认虚拟机主机禁止访问，apache第一个虚拟主机，nginx如果不单独分离虚拟主机配置文件也为第一个，否则就是有“listen 80 default”那个。

13. 设定php禁用函数：php.ini中增加
	
	disable_functions = popen,passthru,escapeshellarg,escapeshellcmd,escapeshellarg,shell_exec,proc_get_status,ini_alter,ini_restore,dl,pfsockopen,openlog,syslog,readlink,symlink,leak,popepassthru,stream_socket_server,popen,proc_open,proc_close,phpinfo

14. 设定
	
	openbase_dir(apache: php_admin_value open_basedir /data/123:/tmp/; php-fpm: php_admin_value[open_basedir]=/data/123:/tmp/)

15. 网站根目录下禁止有test命令的文件如test.php

16. php.ini中关闭display_error

17. php禁止访问远程文件
	
	（php.ini中allow_url_fopen = off; allow_url_include = off）

18. 站点后台访问需要限定ip
	
	（apache http://www.lishiming.net/thread-5365-1-1.html nginx: http://www.lishiming.net/thread-6458-1-1.html )

19. 建议每个站点都配置访问日志，并且做日志切割压缩归档，磁盘空间允许的话，尽量存放比较久的时间

20. 尽量避免开放ftp服务，如果要开放要满足两个原则：1.限定ip访问（iptables实现）；2.密码设置一定要复杂




















