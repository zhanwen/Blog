---
title: 正则表达式学习
date: 2017-11-14 22:25:15
tags:
	- 正则表达式
	- Regular
---

正则表达式是对字符串操作的一种逻辑公式，就是用事先定义好的一些特定字符、及这些特定字符的组合，组成一个“规则字符串”，这个“规则字符串”用来表达对字符串的一种过滤逻辑。

# 正则之grep

	语法：grep [-cinvABC] 'word' filename

	-c:打印符合要求的行数
	-i:忽略大小写
	-n:在输出符合要求的行的同时连同行号一起输出
	-v:打印不符合要求的行
	-A:后跟一个数字（有无空格都可以）例如-A2则表示打印符合要求的行以及下面两行
	-B：后跟一个数字，例如-B2则表示打印符号要求的行以及上面两行
	-C： 后跟一个数字，例如-C2则表示打印符合要求的行以及上下个两行
	-r：会把目录下所有的文件全部遍历
	-w：w为word的意思
<!-- more -->
# grep工具实例：

	//1、过滤出带有某个关键词的行并输出行号
	grep -n 'root' 1.txt
	//2、过滤出不带有某个关键词的行并输出行号
	grep -n -v 'root' 1.txt
	//3、过滤出所有包含数字的行
	grep '[0-9]' 1.txt
	//4、去除所有以‘#’开头的行
	grep -v '^#' 1.txt
	//5、去除所有空行和以'#'开头的行
	grep -v '^$' 1.txt|grep -v'^#'
	//或者是：
	grep -v '^$' |grep -v'^#' 1.txt
	//6、过滤出以英文字母开头的行
	grep '^[a-zA-Z]' 1.txt
	//7、过滤出以非数字开头的行
	grep '^[^0-9]' 1.txt
	//8、过滤出任意一个或多个字符
	grep 'r.o' 1.txt;  r,t之间的任意字符，r，t必须有
	grep 'r*t' 1.txt;  零个或多个r，但是必须有t
	grep 'r.*t' 1.txt; 只要有r和t就行了
	//9、.表示任意一个字符；*表示零个或多个前面的字符；
	//.*表示零个或多个任意字符，空行也包括在内
	//10、指定过滤字符次数
	grep 'o\{2\}' 1.txt

# 正则之egrep
	
	1、egrep工具是grep工具的一个扩展
	2、egrep ‘o+' 1.txt 表示1个或1个以上前面字符
	3、egrep 'o?' 1.txt 表示0个或者1个前面字符
	4、egrep 'roo|body' 1.txt 匹配roo或者匹配body相当于grep -E 'root|body' 1.txt 
	5、egrep 'r(oo)|(at)o' 1.txt用括号表示一个整体，表示roo，或者ato
	6、egrep '(oo)+' 1.txt 表示1个或者多个’oo'

# awk工具
	1、截取文档中的某段
	awk -F ':' '{print $1}' 1.txt
	'{print $0}[打印整个文件
	
	2、也可以使用自定义字符连接每个段
	awk -F ':' '{print $1 "#"$2"#"$3"#"$4}' 1.txt

	3、匹配字符或字符串
	awk '/oo/' 1.txt

	4、针对某个段匹配
	awk -F ':' '$1~/oo/' 1.txt

	5、多次匹配
	awk -F ':' '/root/ {print $1,$3}; $1~/test/;
	$3~/20/' 1.txt

	6、条件操作符==，>,<,!=,>=,<=

	7、awk -F  ':' '$3=="0"' 1.txt

	8、awk -F ':' '$3>="500"' 1.txt

	9、awk -F ':' '$7!="/sbin/nologin"' 1.txt

	10、awk -F ':' '$3<$4' 1.txt

	11、awk -F ':' '$3>"5" && $3<"7"' 1.txt

	12、awk -F ':' '$3>"5" || $7=="/bin/bash"' 1.txt

	13、awk内置变量NF（段数） NR（行数）

	14、head -n3 1.txt |awk -F ':' '{print NF}'

	15、head -n3 1.txt |awk -F ':' '{print $NF}'

	16、head -n3 1.txt |awk -F ':' '{print NR}'

	17、打印20行以后的行awk ’NR>20' 1.txt

	18、awk -F ':' 'NR>20 && $1 ~ /ssh/' 1.txt

	19、更改某个段的值awk -F ':' '$1="root" 1.txt

	20、数学计算，把第三段和第四段值相加，并赋予第七段
	awk -F ':' '{$7=$3+$4; print $0}' 1.txt

	21、计算第三段的总和
	awk -F ':' '{(tot=tot+$3)}; END {print tot}' 1.txt

	22、awk中也可以使用if关键词
	awk -F ':' '{if($1=="root") print$0}' 1.txt

# sed工具
	1、打印指定行
	sed '10'p -n 1.txt;
	sed '1,4'p -n 1.txt;
	sed '5,$'p -n 1.txt;

	2、打印包含某个字符串的行
	sed -n '/root/'p 1.txt可以使用^.*$等特殊符号

	3、-e可以实现同时进行多个任务
	sed  -e '/root/p' -e '/body/p' -n 1.txt也可以用；
	实现sed '/root/p; /body/p' -n 1.txt

	4、删除行
	sed '/root/d' 1.txt ;
	sed '1d' 1.txt;
	sed '1,10d' 1.txt

	5、替换 sed '1,2s/ot/to/g' 1.txt 其中s就是替换的意思，g为全局替换，否则只替换第一次的，/也可以为#，@等

	6、删除所有数字
	sed 's/[0-9]//g' 1.txt

	7、删除所有非数字
	sed 's/[^0-9]//g' 1.txt

	8、调换两个字符串位置
	head -n2 1.txt |sed 's/\(root\)\(.*\)\(bash\)/\3\2\1/'
	3,2,1代表前面的root，.*，和bash，也就是把root 和bash 调换位置
	9、直接修改文件内容
	sed -i 's/ot/to/g' 1.txt






