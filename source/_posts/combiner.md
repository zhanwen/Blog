---
title: Hadoop学习笔记之Combiner
date: 2017-10-17 22:35:25
tags: 
	- Hadoop
	- 大数据
---

1、是在每一个map task的本地运行，能收到map输出的每一个key的valuelist，所以可以做局部汇总处理

2、因为在map task的本地进行了局部汇总，就会让map端的输出数据量大幅精简，减小shuffle过程的网络IO

3、combiner其实就是一个reducer组件，跟真实的reducer的区别就在于，combiner运行maptask的本地

<!-- more -->

4、combiner在使用时需要注意，输入输出KV数据类型要跟map和reduce的相应数据类型匹配

5、要注意业务逻辑不能因为combiner的加入而受影响
