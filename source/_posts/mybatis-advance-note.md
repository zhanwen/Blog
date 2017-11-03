---
title: mybatis学习笔记续二
date: 2017-11-03 13:44:18
tags:
	- Mybatis
	- 数据库
---
Mybatis的应用场景
&emsp;&emsp;对需求变更大的项目，关系数据模型不固定，比如互联网项目，建议使用mybatis，灵活去编写sql语句，对sql语句进行修改、优化方便。

Mybatis开发过程
	
	```
	配置SqlMapConfig.xml（全局配置文件，名称不固定）
	配置XXXMapper.xml映射文件
	根据配置创建SqlSessionFactory,SqlSessionFactory在实际使用时，建议使用单例模式
	根据SqlSessionFactory创建Sqlsession,Sqlsession是一个面向用户的接口，是线程不安全的，最佳应用场合是方法体内,SqlSession提供操作数据库的常用方法，内部使用Exceutor（基本执行器、缓存执行器）底层封装类操作数据库。
	通过Sqlsession操作数据库,对于插入、删除、更新进行事务提交,SqlSesssion使用完毕进行关闭。
	```
<!-- more -->
Mybatis延迟加载
&emsp;&emsp;延迟加载意义：在需求允许的情况下，先查询单表，当需要关联其它表查询时，进行延迟加载，去关联查询，达到目标：不需要关联信息时不查询，需要时再查询。好处：提高数据库的性能。
如果不使用mybatis的延迟加载，可以使用以下方法

```	
	定义两个mapper接口。
	查询订单信息列表
	根据用户id查询用户信息

	查询过程：
	首次查询只调用第一个mapper接口（查询订单信息列表）
	当需要查询某个订单的用户信息时，从订单信息中获取用户id，调用第二个mapper（根据用户id查询用户信息）。
```

使用Mybatis的延迟加载，过程如下
打开延迟加载开关，在SqlMapConfig.xml中配置setting全局参数：
```	
	lazyLoadingEnabled：延迟加载的总开关，设置为true
	aggressiveLazyLoading：设置为false，实现按需加载（将积极变为消极）
	<settings>
		<setting name='lazyLoadingEnabled' value='true'></setting>
		<setting name='aggressiveLazyLoading' value='false'></setting>
	</settings>
```
配置延迟加载:
	
	要根据订单信息中的用户id(外键)查询用户信息，修改订单信息查询的statement。将resultType改为resultMap，定义resultMap，配置延迟加载：然后在resultMap中添加需要延迟加载的配置，通过<associattion property='' javaType='' select='xxxmethod' column='要传入的参数值'/>
	

使用association关联查询单个对象和使用collection关联查询集合对象都支持延迟加载。

Mybatis缓存
&emsp;&emsp;将从数据库中查询出来的数据缓存起来，缓存介质：内存、磁盘，从缓存中取数据，而不从数据库查询，减少了数据库的操作，提高了数据处理性能。

一级缓存，Mybatis默认提供一级缓存，缓存范围是一个sqlSession。在同一个SqlSession中，两次执行相同的sql查询，第二次不再从数据库查询。但是如果第一次查询后，执行commit提交，mybatis会清除缓存，第二次查询从数据库查询。

一级缓存的原理：一级缓存采用Hashmap存储，mybatis执行查询时，从缓存中查询，如果缓存中没有从数据库查询。如果该SqlSession执行commit()提交，清除缓存
	
	Map的key：（code+。。。statement的id+sql+输入参。。）

二级缓存，缓存范围是跨SqlSession的，范围是mapper的namespace，相同的namespace使用一个二级缓存结构。需要进行参数配置让mybatis支持二级缓存。在核心配置文件SqlMapConfig.xml中加入，表示打开二级缓存开关
	
	<setting name="cacheEnabled" value="true"/>

还需要在mapper.xml中配置是否打开该mapper的二级缓存。
	
	<cache/>	//配置二级缓存

二级缓存注意事项：
	
	1、实现序列化
	2、如何清除缓存。执行相同的statement，如果执行提交操作需要清除二级缓存。如果想让statement执行后刷新缓存（清除缓存），在statement中设置flushCache="true" （默认值 是true）
	3、设置statement是否开启二级缓存。如果让某个statement启用二级缓存，设置useCache=true（默认值为true

二级缓存原理：如果二缓存开启，首先从二级缓存查询数据，如果二级缓存有则从二级缓存中获取数据，如果二级缓存没有，从一级缓存找是否有缓存数据，如果一级缓存没有，查询数据库。

二级缓存应用场景
1、针对复杂的查询或统计的功能，用户不要求每次都查询到最新信息，使用二级缓存，通过刷新间隔flushInterval设置刷新间隔时间，由mybatis自动刷新。比如：实现用户分类统计sql，该查询非常耗费时间。将用户分类统计sql查询结果使用二级缓存，同时设置刷新间隔时间：flushInterval（一般设置时间较长，比如30分钟，60分钟，24小时，根据需求而定。

2、针对信息变化频率高，需要显示最新的信息，使用二级缓存。将信息查询的statement与信息的增、删、改定义在一个mapper.xml中，此mapper实现二级缓存，当执行增、删、修改时，由mybatis及时刷新缓存，满足用户从缓存查询到最新的数据。比如：新闻列表显示前10条，该查询非常快，但并发大对数据也有压力。
将新闻列表查询前10条的sql进行二级缓存，这里不用刷新间隔时间，当执行新闻添加、最佳的方案使用页面缓存。



















