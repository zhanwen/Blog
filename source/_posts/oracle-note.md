---
title: Oracle数据库操作
date: 2017-11-05 22:09:46
tags:	
	- Oracle
	- 数据库
---
查看所有的表    
    
    select * from tab;

对于出错的sql语句，可以通过`数字`进行定位，然后使用--c（change）来改变对应出错的地方。
    
    c /form/from

使用/命令是执行上一条命令
	
	／
<!-- more -->
null值的注意事项：
    
    1、包含null的表达式都为null
    2、null永远不等于null
    3. 如果集合中，含有null，不能使用not in；但是可以使用in
    4. null的排序
    5. 组函数自动滤空;可以嵌套滤空函数来屏蔽他的滤空功能

	1、对于要处理null的表达式，可以使用nvl(a,b),若a不为null，则返回a的值
	2、对于要选择null的列，如果使用column=null则一条也不能查询出来，而要选择column is null才可以选择null的列。
	3、in, not in 本质上是逻辑or运算，in(id=1 or id=2 or id=null), not(id=1 or id=2, id=null), 其中 False or Null == Null, True or Null == True
	4. null的升序是没有问题的，降序的时候null最大，会排在最前面，可以使用nulls last命令将其排在后面。

edit命令，执行edit（ed）命令之后，在windows上会将上一条sql通过记事本打开，在linux上则会通过vi打开。

对于使用别名当中，若有特殊字符或者关键字，需要加上双引号。

distinct作用于后面的所有列。

连接符 --concat
   
    select concat('a','b') from dual; dual表跟任何表都没有关系。
    select concat(1+3) from dual;     dual(伪表)是为了满足语法的要求。
    
    select 'a'||'b' 字符串 from dual;  //||相当于加号
    结果如下：
    	字符串
    	a b

字符串
    
    日期和字符只能在单引号中出现

sql和sqlplus
    
    sql（select, insert, update, delete）不能缩写
    sqlplus(ed(edit), c(change), format(for), col(column), desc) 可以缩写
    iSQL*Plus其实就是sqlplus的网页版，oracle 9i,10g支持（不安全，协议http）。11g,12c不支持
        表述表结构、
        编辑SQL语句、
        执行SQL语句、
        将SQL保存在文件并将SQL语句执行结果保存在文件中、
        在保存的文件中执行语句、
        将文本文件装入SQL*Plus编辑窗口

查看状态
	
	lsnctl status 

清屏

	host cls 

录制操作，可以记录操作的内容
	
	spool xxx.txt，spool of 

追加，后面的空格必须是两个空格
	
	apppend 

注释
	
	--

查询注意
    
    字符串大小写敏感 
    日期格式敏感

修改日期格式    
    
    select * from v$nls_parameters;
    alter session set NLS_DATE_FORMAT='yyyy-mm-dd'; //session只在当前会话有效

between......and 
   
    1. 含有边界  2. 小值在前,大值在后

in  在集合中

like 模糊查询 
	
	like 'S%'; '_'是占位符，比如姓名为三个字，like '___'，对于有特殊字符的需要进行转义。比如 like '%\_%' escape '\'

oracle 自动开启事务

order by
    
    后面可以加，列，表达式，别名，序号(select的第几列)
    作用于后面所有的列，先按照第一个列排序，如果相同，再按照第二列排序；以此类推。
    desc 只作用于离他最近的一列


sql优化
    
    1、*和字段名
    2、where 从右往左，先判断右边的在判断左边的
    3、尽量使用where
























