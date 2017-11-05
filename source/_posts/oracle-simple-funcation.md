---
title: Oracle单行函数
date: 2017-11-05 22:18:33
tags:
	- Oracle
	- 数据库
---
字符函数
    
    lower('Hello World') 小写
    upper('Hello World') 大写
    initcap('hello world') 首字母大写
    substr(a,b) 从a中，第b位开始取子串
    substr(a,b,c) 从a中，第b位开始取,取c位
    length 字符数  lengthb 字节数
    instr(a,b) 在a中，查找b
    lpad 左填充  rpad 右填充。
        lpad('abcd',10,'@') //@@@@@@@@@@abcd 
        rpad('abcd',10,'@') //abcd@@@@@@@@@@
    trim 去掉前后指定的字符
        trim('H' from 'Hello World')
    replace 替换 replace('Hello World','l','*')

<!-- more -->
数值函数

    round 四舍五入
        round(56.926,2)     56.93
        round(56.926,1)     56.9
        round(56.926,0)     57
        round(56.926,-1)    60
        round(56.926,-2)    100
    trunc 截断
    MOD 求余

日期函数

    当前时间
        select sysdate from dual;
    格式化显式一个时间
        select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') from dual;
    昨天、今天、明天            
        sysdate-1、sysdate、sysdate+1
    日期段
        months_between
        months_between(sysdate,employtime) //精确值
    日期相加，不支持两个日期相加(+)
        add_months 
        add_months(sysdate,22)
        to_char(sysdate,'yyyy-mm-dd hh24:mi:ss"今天是"day')
    本月的最后一天
        last_day
        last_day(sysdate)
    下一个星期五
        next_day
        next_day(sysdate,'星期五')
    两位小数，千位符，货币代码
        to_char(salary,'L9,999.99')
    日期四舍五入
        round   
        round(sysdate,'month')、round(sysdate,'year') //month看day，year看month
    
    nvl2(a,b,c) 
        当a=null时候，返回c；否则返回b，相当于三元运算符
    
    nullif(a,b)
        当a=b时候，返回null，否则返回a
    
    coalesce
        从左到右找到第一个不为null的值并返回
    
判断

    select name,job,salary 涨前,
       case job when 'PRESIDENT' then salary+1000
                when 'MANAGER' then salary+800
                else salary+400
        end 涨后
    from emp;

    select name,job,salary 涨前,
       decode job, 'PRESIDENT', sal+1000,
                    'MANAGER',  sal+800,
                                sal+400) 涨后
    from emp;

next_day的应用
   
    每个星期一自动备份数据
    1、分布式数据库
    2、快照
