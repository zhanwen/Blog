---
title: 文本编辑工具Vim
date: 2017-11-15 22:17:20
tags:
	- Vim
	- Linux
---

若是使用下面的命令提示没有该命令，可以先进行安装，安装Vim命令如下，Vim官网vim.wendal.net 

	yum install -y vim-enhanced

# 一般模式

	！$ 使用上面的命令

	：set nu 显示行号。
<!-- more -->
	h向左
	j向下
	k向上
	l向右

	ctrl +f 下翻
	ctrl +b 上翻

	shift +4本行行尾
	shift +6本行行首 或者是数字0
	gg首行
	G尾行
	nG移动到第几行，（n为任意数字）

	dd 删除/剪切光标所在的那一行
	p  粘贴，在下一行粘贴，P在上一行粘贴
	u 返回  ctrl + r 反着还原
	yy 复制光标所在行
	ndd 剪切n行
	nyy 复制n行
	x表示向后删除/剪切一个字符，X表示向前删除/剪切一个字符
	nx向后删除n个字符

	加上+号这个选项，可以光标到最后一行
	加上+5 表示在第几行
	比如：vim +4 /etc/init.d/iptables

# 命令模式

	/xxxx查找某个xxx
	按n是向下走

	？xxx查找某个xxx
	按n是向上走

	：n1，n2s /word1/word2/g
	在n1-n2行之间查找word1并替换word2，不加g则只替换每行的第一个word1
	s代表替换

	：1,$s/word1/word2/g
	将文档所有的word1替换为word2，不加g则只替换每行的第一个word1

	：加向上箭头，翻以前使用的命令

# 编辑模式
	O在上一行插入
	o在下一行插入
	写完之后按esc

	a 光标后插入
	i 在光标前插入
	I 在行首插入
	A在行尾插入































