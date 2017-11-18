---
title: shell脚本综合练习
date: 2017-11-18 23:06:17
tags:
	- Linux
	- Shell
---
# 什么是Shell

> 1、它是一种脚本语言，并非编程语言
2、可以使用一些逻辑判断、循坏等语法
3、可以自定义子函数
4、是系统命令的集合
5、shell脚本可以实现自动化运维，大大增加我们的工作效率

# shell脚本的结构及执行

> 6、开头行指定bash路径：#！/bin/bash
7、脚本的名字以.sh结尾，用于区分这是一个shell脚本
8、执行方式有两种：
chmod +x 1.sh; ./1.sh如果没有执行权限可以bash 1.sh
9、bash -x 1.sh 可以查看脚本执行过程

脚本默认放在这个目录：
/usr/local/sbin/

<!-- more -->
# shell脚本中的变量

> 1、当脚本中使用某个字符串频繁并且字符串长度很长时就应该使用变量替代
2、使用条件语句时，变量是必不可少的
3、引用某个命令的结果时，用变量替代
4、写和用户交互的脚步时，变量也是必不可少的
5、内置变量$0,$1,$2......
6、数学运算a=1；b=2；
c=$(($a+$b))或者$[$a+$b]

# shell中的逻辑判断

用（（））这个就要加；[]这个不需要加分号

格式1：
	
	if条件；then语句；fi

格式2：
	
	if条件；then语句；else语句；fi

格式3：

	if...；then...；elif...；then...；else...； fi

逻辑判断表达式：

	if[$a -gt $b ]; if [$a -lt 5 ];
	if[ $b -eq 10 ]等
	-gt(>); -lt(<); -ge(>=);
	-le(<=); eq(==); -ne(!=)	//注意到处都是空格

可以使用&& || 结合多个条件

if 判断文件、目录属性
> 1、[ -f file ]判断是否是普通文件，且存在
2、[ -d fiel ]判断是否是目录，且存在
3、[ -e file ]判断文件或目录是否存在
4、[ -r file ]判断文件是否刻度
5、[ -w file ]判断文件是否可写
6、[ -x file ]判断文件是否可执行

netstat -lnp打印当前系统开放端口的

if判读一些特殊用法
> 1、if [ -z $a ] 这个表示当变量a 的值为空时会怎么样，表示为空
2、if grep -q '123' 1.txt; then 表示如果1.txt中含有‘123’的行时会怎么样，q相当quite
3、if [ ! -e file ]; then 表示文件不存在时会怎么样
4、if (($a<1)); then ... 等同于if [$a -lt 1 [; then ... [] 中不能使用<,>,==,!=,>=,<=这样的符号

如果不想它输出来，就把它重定向到/dev/null中。

# shell中的case判断
	case 变量 in
	value）
	    command
	    ；；
	value2）
	    command
	    ；；

	value3）
	    command
	    ；；
	*)
	     command
	    ；；
	esac

# shell中的循环

	for循环 语法结构: 
	for 变量名 in 条件； do......
	done

	while循环语法结构：
	while 条件；do……
	done
	死循环用：表示

	break直接结束本层循环；
	continue忽略continue之下的代码，直接进行下一次循环

	exit 直接退出shell

# shell脚本中的函数

> 1、函数就是把一段代码整理到了一个小单元中，并给这个小单元起一个名字，当用到这段带啊时直接调用这个小单元名字即可
2、格式：
    function f_name(){
        command
    }
3、函数必须要放在最前面
4、函数里可以export全局变量









