---
title: Mybatis学习笔记
date: 2017-11-01 22:57:40
tags:	
	- Mybatis
	- 数据库
---

&emsp;&emsp;MyBatis本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis。MyBatis是一个优秀的持久层框架，它对jdbc的操作数据库的过程进行封装，使开发者只需要关注SQL本身，而不需要花费精力去处理例如注册驱动、创建connection、创建statement、手动设置参数、结果集检索等jdbc繁杂的过程代码。Mybatis通过xml或注解的方式将要执行的statement配置起来，并通过java对象和statement中的sql进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射成java对象并返回。

<!-- more -->

引入Mybatis之前，我们先看下传统的JDBC的不雅之处
	

	1、将sql语句硬编码到java代码中，如果修改sql语句，需要修改java代码，重新编译。系统可维护性不高。设想如何解决？能否将sql单独 配置在配置文件中。
	
	2、数据库连接频繁开启和释放，对数据库的资源是一种浪费。设想如何解决？使用数据库连接池管理数据库连接。

	3、向preparedStatement中占位符的位置设置参数时，存在硬编码（占位符的位置，设置的变量值）设想如何解决？能否也通过配置的方式，配置设置的参数，自动进行设置参数。

	4、解析结果集时存在硬编码（表的字段名、字段的类型）设想如何解决？能否将查询结果集映射成java对象。


可以看到上面这几条对于我们来说还是不太友好了，引入mybatis之后，这些问题就会迎刃而解。先看下mybatis配置文件


	1、mybatis.xml，此文件作为mybatis的全局配置文件，配置了mybatis的运行环境等信息。mapper.xml文件即sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在mybatis.xml中加载。 
	
	2、通过mybatis环境等配置信息构造SqlSessionFactory即会话工厂

	3、由会话工厂创建sqlSession即会话，操作数据库需要通过sqlSession进行。
	
	4、mybatis底层自定义了Executor执行器接口操作数据库，Executor接口有两个实现，一个是基本执行器、一个是缓存执行器。
	
	5、Mapped Statement也是mybatis一个底层封装对象，它包装了mybatis配置信息及sql映射信息等。mapper.xml文件中一个sql对应一个Mapped Statement对象，sql的id即是Mapped statement的id。
	
	6、Mapped Statement对sql执行输入参数进行定义,包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql前将输入的java对象映射至sql中，输入参数映射就是jdbc编程中对preparedStatement设置参数。
	
	7、Mapped Statement对sql执行输出结果进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql后将输出结果映射至java对象中，输出结果映射过程相当于jdbc编程中对结果的解析处理过程。



