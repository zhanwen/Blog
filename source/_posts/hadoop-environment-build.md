---
title: Hadoop环境搭建
date: 2017-10-14 22:30:51
tags: 
     - Hadoop
     - 大数据

---

# 1. 准备Linux环境
     1.1 修改主机名
		    vim /etc/sysconfig/network
		
		    NETWORKING=yes
		    HOSTNAME=yun

	   1.2 修改IP
         修改配置文件方式
          vim /etc/sysconfig/network-scripts/ifcfg-eth0

          DEVICE="eth0"
          BOOTPROTO="static"           
          ONBOOT="yes"
          IPADDR="192.168.80.190"           
          NETMASK="255.255.255.0"          
          GATEWAY="192.168.80.88"            

	    1.3 修改主机名和IP的映射关系
	      	vim /etc/hosts
			
		      192.168.80.190	yun
	
        1.4 关闭防火墙
          #查看防火墙状态
          service iptables status
          #关闭防火墙
          service iptables stop
          #查看防火墙开机启动状态
          chkconfig iptables --list
          #关闭防火墙开机启动
          chkconfig iptables off
	
	     1.5重启Linux
		      reboot 或者 init 6

<!--  more  -->

# 2. 安装JDK
	  2.1 上传alt+p 后出现sftp窗口，然后put jdk路径
	
      2.2 解压jdk
        #创建文件夹
        mkdir /home/hadoop/app
        #解压
        tar -zxvf jdk-xxxx.tar.gz -C /root/xxx
		
      2.3 将java添加到环境变量中
        vim /etc/profile
        #在文件最后添加
        export JAVA_HOME=xxxxx
        export PATH=$PATH:$JAVA_HOME/bin

        #刷新配置
        source /etc/profile
		
# 3. 安装hadoop2.4.1
        先上传hadoop的安装包到服务器上去/root/hadoop/
        注意：hadoop2.x的配置文件$HADOOP_HOME/etc/hadoop
        伪分布式需要修改5个配置文件
        3.1 配置hadoop
        第一个：hadoop-env.sh
          vim hadoop-env.sh
          #第27行
          export JAVA_HOME=/xxx/xxx/xxx

        第二个：core-site.xml

          <!-- 指定HADOOP所使用的文件系统schema（URI），HDFS的老大（NameNode）的地址 -->
          <property>
            <name>fs.defaultFS</name>
            <value>hdfs://xxxx:8000</value>
          </property>
          <!-- 指定hadoop运行时产生文件的存储目录 -->
          <property>
            <name>hadoop.tmp.dir</name>
            <value>/xxx/xxx/hadoop-2.4.1/tmp</value>
          </property>

        第三个：hdfs-site.xml   hdfs-default.xml  (3)
          <!-- 指定HDFS副本的数量 -->
          <property>
            <name>dfs.replication</name>
            <value>1</value>
          </property>

        第四个：mapred-site.xml (mv mapred-site.xml.template mapred-site.xml)
          mv mapred-site.xml.template mapred-site.xml
          vim mapred-site.xml
          <!-- 指定mr运行在yarn上 -->
          <property>
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
          </property>

        第五个：yarn-site.xml
          <!-- 指定YARN的老大（ResourceManager）的地址 -->
          <property>
            <name>yarn.resourcemanager.hostname</name>
            <value>hostname</value>
          </property>
          <!-- reducer获取数据的方式 -->
          <property>
            <name>yarn.nodemanager.aux-services</name>
            <value>mapreduce_shuffle</value>
           </property>

        3.2 将hadoop添加到环境变量

        vim /etc/proflie
          export JAVA_HOME=/xxxx/xxx/jdk
          export HADOOP_HOME=/xxxx/hadoop-2.4.1
          export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

        source /etc/profile

        3.3 格式化namenode（是对namenode进行初始化）
          hdfs namenode -format (hadoop namenode -format)

        3.4 启动hadoop
          先启动HDFS
          sbin/start-dfs.sh

          再启动YARN
          sbin/start-yarn.sh

        3.5 验证是否启动成功
          使用jps命令验证
          54212 NameNode
          12345 Jps
          32142 SecondaryNameNode
          23672 NodeManager
          34712 ResourceManager
          26341 DataNode

          http://xxxx:50070 （HDFS管理界面）
          http://xxxx:8088 （MR管理界面）
		
# 4.配置ssh免登陆
      #生成ssh免登陆密钥
      #进入到我的home目录
      cd ~/.ssh

      ssh-keygen -t rsa （四个回车）
      执行完这个命令后，会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）
      将公钥拷贝到要免登陆的机器上
      ssh-copy-id localhost

# 5. 安装过程中出现错误解决
    错误：localhost: /home/hadoop/hadoop/sbin/slaves.sh: line 60: ssh: command not found
    解决：安装openssh-client或者openssh-clients

