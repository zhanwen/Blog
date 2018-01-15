---
title: 去除Tensorflow的警告信息
date: 2018-01-15 10:54:27
tags:
	- Python
	- Tensorflow
---

使用`tensorflow`运行程序会提示以下警告信息，但是并不影响运行。警告信息如下

	2018-01-15 10:56:22.537770: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use SSE4.2 instructions, but these are available on your machine and could speed up CPU computations.
	2018-01-15 10:56:22.537796: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use AVX instructions, but these are available on your machine and could speed up CPU computations.
	2018-01-15 10:56:22.537802: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use AVX2 instructions, but these are available on your machine and could speed up CPU computations.
	2018-01-15 10:56:22.537809: W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use FMA instructions, but these are available on your machine and could speed up CPU computations.

这个警告是告诉我们可以用自己机器的CPU来进行计算，会得到更好的性能。原因是我直接使用`pycharm`里面的`plugs`直接安装的，所以没有去安装对应的模块。去除这个警告我们可以有两种方法，一种是忽略这个警告信息，另一种就是根据提示信息，使用`CPU`进行计算。这里给出第一种的解决方法。第二种的解决方法大家可以去搜索一下。
<!-- more -->
**解决办法：**
	
	# 在代码的开始加入下面两行代码
	# 也就是对应的日志级别，1 代表是 info， 2 代表是 warning
	# 改为 warning 就可以忽略上面的警告信息 
	import os
	os.environ['TF_CPP_MIN_LOG_LEVEL']='2'
