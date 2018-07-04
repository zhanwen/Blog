---
title: Mybatis学习笔记续
date: 2017-11-02 22:10:23
tags:
	- Mybatis
	- 数据库
---

# Mapper动态代理方法
只需要写dao接口(Mapper)，而不需要写dao实现类，由mybatis根据dao接口和映射文件中statement的定义生成接口实现代理对象。可以调用代理对象方法。
Mybatis官方建议：将dao接口叫做mapper。

目标：通过一些规则让mybatis根据dao接口和映射文件中statement的定义生成接口实现代理对象，mybatis将以下代码自动在代理对象实现：

	User user = sqlSession.selectUser("findUserById", id);

为了让XXXmapper.xml和XXXmapper.java对应起来，将XXXmapper.xml中的namespace设置为XXXmapper.java的全限定名（包括包名）。

<!-- more -->

Mybatis生成代理对象时，根据statement的标签决定调用SqlSession的方法(select、insert、update..)，根据上边接口方法返回值,类型来决定是调用
selectOne还是selectList，如果返回的是单个对象，动态代理调用selectOne()，如果返回的是集合对象，动态代理调用selectList()。

Mapper开发规则

	```
	在XXXmapper.xml中将namespace设置为mapper.java的全限定名
	将XXXmapper.java接口的方法名和XXXmapper.xml中statement的id保持一致。
	将XXXmapper.java接口的方法输入参数类型和XXXapper.xml中statement的parameterType保持一致
	将XXXmapper.java接口的方法输出结果类型和XXXmapper.xml中statement的resultType保持一致
	```

# Mybatis配置文件
SqlMapConfig.xml作为mybatis的全局配置文件，配置内容包括：数据库环境、mapper定义、全局参数设置。
	
	properties（属性）
	settings（全局配置参数）
	typeAliases（类型别名）
	typeHandlers（类型处理器）
	objectFactory（对象工厂）
	plugins（插件）
	environments（环境集合属性对象）
	environment（环境子属性对象）
	transactionManager（事务管理）
	dataSource（数据源）
	mappers（映射器）

typeAliases（类型别名）
	
	在parameterType和resultType设置时，为了方便编码，定义别名代替pojo的全路径。

typeHandlers（类型处理器）
	
	类型处理器用于java类型和jdbc类型映射.

parameterType(输入类型)

	parameterType：用于设置输入参数的类型

resultType(结果类型)

	将sql查询结果集映射为java对象。要求sql查询的字段名和resultType指定pojo的属性名一致，才能映射成功。如果全部字段和pojo的属性名不一致，映射生成的java对象为空，只要有一个字段和pojo属性名一致，映射生成的java对象不为空。
结论：sql查询字段名和pojo的属性名一致才能映射成功。当然也可以返回简单类型，比如int，string。

resultType和resultMap区别
	
	resultType：sql查询结果集使用resultType映射，要求sql查询字段名和pojo的属性名一致，才能映射成功。
	resultMap： 如果sql查询结果集的字段名和pojo的属性名不一致，使用resultMap将sql查询字段名和pojo的属性作一个对应关系 ，首先需要定义resultMap，最终要使用pojo作为结果集映射对象。

不管select返回的是单个 对象还是集合对象，resultType要指定单条记录映射的java对象。

# \#{}与${}的区别
&emsp;&emsp;\#{}：表示占位符，如果获取简单类型，#{}中可以使用value或其它名称。有效防止sql注入使用#{}设置参数无需考虑参数的类型。 比如使用#{}比较日期字段，
    
    select * from tablename where birthday >=#{birthday}

&emsp;&emsp;${}：表示sql拼接，如果获取简单类型，#{}中只能使用value。${}无法防止sql注入。使用${}设置参数必须考虑参数的类型，比如：使用oracle查询条件是日期类型，如果使用${}，必须人为将${}两边加单引号通过to_date转日期。

	Select * from table where birthday >=to_date(‘${birthday}’,’yyyy-MM-dd’)

在没有特殊要求的情况下，建议使用#{}占位符,有些情况必须使用${}，比如：需要动态拼接表名
	
	Select * from ${tablename}

动态拼接排序字段：
	
	select * from tablename order by ${username} desc


# 包装对象的使用
为了方便查询，我们通常不只是查询一个pojo中的属性，还有可能会查询其它的条件，比如数组或者集合，这时我们可以通过包装对象来实现。将我们要查询的条件封装到包装对象中，然后在我们调用XXXMap.java的时候直接传入我们的参数即可。

当sql语句比较多的时候，会发现有很多重复的where条件，这时我们可以通过抽取sql，转换为动态sql(比如if)或者sql片段。





















