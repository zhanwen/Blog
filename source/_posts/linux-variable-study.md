---
title: linux下的变量以及系统和个人环境变量的配置文件
date: 2017-11-18 22:06:28
tags:
	- Linux
---
> 1、系统变量名都是大写，echo可以查看变量名
2、env可以列出当前用户的所有环境变量以及用户自定义全局变量
3、set命令可以把所有变量列出来包括系统的和自定义的全局变量以及当前shell自定义变量
4、linux下设置自定义变量规则：

执行#bash进入当前用户的子shell，可以用<font color="red">pstree</font>查看

(1) 格式为“a=b”，其中a为变量名，b为变量的内容，等号两边不能有空格
(2) 变量名只能有英、数字以及下划线组成，而且不能以数字开头
(3) 当变量内容带有特殊字符（如空格）时，需要加上单引号
(4) 如果变量内容中需要用到其他命令运行结果则可以使用反引号
(5) 变量内容可以累加到其他变量的内容，需要加双引号

5、系统所有用户使用变量：export myname=hanwen 全局变量，加入/etc/profile并source /etc/profile永久生效

6、系统某个用户使用变量：export myname=hanwen加入当前用户家目录下的.bashrc中source .bashrc
vim ~/.bashrc
source .bashrc

7、export myname=hanwne 全局变量，export不加任何选项表示，声明所有的环境变量以及用户自定义变量

8、用户自定义变量，可以使用unset变量名进行解除变量设置
















