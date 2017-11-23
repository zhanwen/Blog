---
title: linux下数据备份工具rsync
date: 2017-11-23 23:03:24
tags:
	- Linux
---

	1、scp 1.txt /tmp #拷贝本地的数据，这和cp一样
	2、scp [-r] /dir/ user@ip:/dir/ #拷贝到远程，也类似于cp
	3、rsync的优点在于，可以增量备份，就是说目标机器上已经存在某个文件，且和源文件一样，那它不会覆盖该文件。
	4、rsync 不仅可以像cp一样在本地同步数据，还可以同步到远程
	5、没有rsync命令，使用yum -install -y rsync安装
	6、rsync -av 123.txt /tmp/
	7、rsync -av 123.txt root@192.168.238.22：/tmp/ #其中root@可以省略掉，回车后会提示让输入目标机器的root密码

<!-- more -->
rsync命令格式

	rsync [option]... SRC DEST
	rsync [option]... SRC [USER@]HOST:DEST
	rsync [option]... [USER@]HOST:SRC DEST
	rsync [option]... [USER@]HOST::SRC DEST
	rsync [option]... SRC [USER@]HOST::DEST

rsync常用选项

    -a：归档模式，表示以递归方式传输文件，并保持所有属性，等同于-riptgoD，-a选项后面可以跟一个--no-OPTION这个表示关闭-riptgoD中的某一个例如-a--no-l等同于-rptgoD
	-r 对子目录以递归模式处理，主要是针对目录来说的，如果单独传一个文件不需要加-r，但是传输的是目录必须加-r选项
	-v 打印一些信息出来，比如速率，文件数量等
	-l  保留软链接
	-L  向对待常规文件一样处理软链接，如果是SRC中有软链接文件，则加上该选项后将会把软链接指向的目标文件拷贝到DST
	-p  保持文件权限
	-o  保持文件爱你属住信息
	-g 保持文件属组信息
	-D 保持设备文件信息
	-t 保持文件时间信息
	--delete 删除那些DST中SRC没有的文件
	--exclude=PATTERN 指定排除不需要传输的文件，等号后面跟文件名，可以是万用字符模式（如*.txt）
	--progress 在同步的过程中可以看到同步的过程状态，比如统计要同步的文件数量、同步的文件传输速度等等
	-u显示同步过程的详细信息，--exclude后面也可以使用通配符 加上这个选项后将会把DST中比SRC还新的文件排除掉，不会覆盖

最常用的

    -a，-v，--delete， --exclud，-L，-u，--progress

选项讲解

> 1、rsync -av dir/ dir2/ #其中dir2/目录可以不存在，记得同步目录时一定要在末尾加上/
2、-a会把软链接原原本本的拷贝过去，那有时候我们想拷贝源文件怎么办？就是用到一个-L

> 3、rsync -avL test1/ test2/

4、-u 选项的作用是，如果目标文件比源文件新，那么会忽略掉该文件

	touch test2/1.txt； rsync -avu test1/ test2/

> 5、rsync -av --delete test1/ test2/ #这样会把test2目录比test1/ 目录多出来的文件删除掉

> 6、rsync -a --exclude="2.txt" test1/ test2/ #在同步的过程中，会忽略掉2.txt这个文件

> 7、rsync -a --progress --exclude="*.txt" test1/ test2/ #--progress 显示同步过程的详细信息，--exclude后面也可以使用通配符*




















