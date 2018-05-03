---
title: Mac下安装Django并验证是否安装成功
date: 2018-05-03 20:54:44
tags:
		- Django
		- Python
---
1. 登录`Django`官网[或者通过这里](https://github.com/django/django/releases)下载需要的文件，`Mac`下面我们可以下载对应的`tar.gz`文件。<font color='red'>注：</font>在下载`Django`的时候要注意下载的版本和`python`版本之间的支持。

2. 将下载的文件移动到我们需要安装的目录中，自己可以放在一个单独存放软件的文件夹下（这里不一定是系统默认的安装路径）可以自己新建。在`shell`命令行中进入下载好的`django-2.0.tar.gz`文件目录中，执行下面的命令，进行解压。  
		
		tar -zxvf django-2.0.tar.gz
<!-- more -->
3. 解压之后，会在该目录出现`django-2.0.5`文件夹，然后我们进入该文件夹，执行安装。
		
		python setup.py install
4. 安装完成之后，我们设置下环境变量，因为不设置环境变量的话，在后面执行的话会出现一些小的问题。

5. 编辑`.bashrc`或者是`ect/profile`文件，在文件中加入我们的`Django`安装目录，在查看安装目录的时候，我们可以使用`pwd`来查看当前的路径，即可显示当前目录，加入下面两行即可。 
		
		export DJANGO_HOME=/Users/zhanghanwen/Tools/django-2.0.5/django
		export PATH=$PATH:$DJANGO_HOME/bin
6. 至此，我们的安装就完成了，接下来我们在`python`命令行中进行验证是否安装成功。
7. 在命令行中输入`python`进入`python`环境下
		
		>>> import django
		>>> django.get_version()
		'2.0.5'
8. 返回正确的版本，说明我们安装成功。
		
