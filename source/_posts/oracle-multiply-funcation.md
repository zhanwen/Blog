---
title: Oracle多行函数
date: 2017-11-05 21:21:49
tags:
	- Oracle
	- 数据库
---
多行函数
   
    sum
    count(*)
    平均值
        1、avg(column)                 //组函数自动滤空
        2、sum(column)/count(*)        
        3、sum(column)/count(column)   //count(column)不为null的记录
    
    按分组求平均值的时候，先求出平均值，在分组即可。
    
    where后面不能使用组函数而having可以，where也要在group by前面，having是在group by之后。
    
    where先过滤在分组
    having先分组在过滤
    
    group by rollup(a,b)
        group by a,b + group by a + group by null
<!-- more -->
多表查询

    等值连接
    不等值连接
    外连接
    对于某些不成立的记录，仍然希望包含在最后的结果中
        左外连接：当where e.deptno=d.deptno不成立的时候，等号左边的表仍然被包含
            where e.deptno=d.deptno(+)
        右外连接：当where e.deptno=d.deptno不成立的时候，等号右边的表仍然被包含
            where e.deptno(+)=d.deptno
    自连接：通过表的别名，将同一张表视为多张表，自连接不适合操作大表