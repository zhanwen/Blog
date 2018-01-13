---
title: hadoop-spark-hbase集群搭建   
date: 2017-12-27 22:47:58
tags:
	- Hadoop
	- Hbase
	- Spark
	- Hive
	- Mysql
	- 集群 
	  
---

根据项目需求我们搭建了一套`Hadoop` + `Spark` + `Hbase` + `Hive`的架构方案。目前服务器总共有六台，每台服务器有五块硬盘，大小分别是`300G`、`300G`、`4T`、`4T`、`4T`，各个服务器主机名分别为`Cloud`、`Cloud2`、`Cloud3`、`Cloud4`、`Cloud5`、`Cloud6`。具体使用情况分别如下表：

|	  | Cloud      | Cloud2  | Cloud3  | Cloud4  | Cloud5  | Cloud6  |
| ------------  |:-------------:| -----:|-----:|-----:|-----:|-----:|
| `300G` |  系统 |  系统   |  系统  | 系统  | 系统  | 系统  |
| `300G` |  软件 |  软件   | 软件  | 软件  | 软件  | 软件  |
| `4T` |  存储数据 |  存储数据   |  存储数据  | 存储数据  | 存储数据  | 存储数据  |
| `4T` |  存储数据 |  存储数据   |  存储数据  | 存储数据  | 存储数据  | 存储数据  |
| `4T` |  备份  |  备份   |  备份  | 备份  | 备份  | 备份  |

每台服务器都是`centos7`的系统，配置完全相同。项目架构所需要安装的软件和各个服务器上所运行的服务具体情况如下表，由于学校不能申请过多的临时`IP`地址，所以这里先使用内网地址配置，后期会更改为正式`IP`。
<!-- more -->

|	  | Cloud      | Cloud2  | Cloud3  | Cloud4  | Cloud5  | Cloud6  |
| ------------  |:-------------:| -----:|-----:|-----:|-----:|-----:|
| `IP`      | `192.168.1.100` | `192.168.1.101` | `192.168.1.102` | `192.168.1.103` | `192.168.1.104` | `192.168.1.105`|
| 安装的软件      | jdk hadoop hbase spark hive       |   jdk hadoop spark hbase |  jdk hadoop spark hbase |  jdk hadoop zookeeper |  jdk hadoop zookeeper  |  jdk hadoop zookeeper |
| `hadoop`的服务 | Namenode DFSZKFailoverController  | Namenode DFSZKFailoverController | ResourceManager | DataNode NodeManager JournalNode | DataNode NodeManager JournalNode |   DataNode NodeManager JournalNode |
| `hbase`的服务 | HMaster    |    HRegionServer |   HRegionServer |    |    |   |
| `spark`的服务 | Master      |    Worker |   Worker |    |    |    |
| `zookeeper`的服务 |       |     |    |   QuorumPeerMain |   QuorumPeerMain |   QuorumPeerMain |
| `Tomcat`的服务 |    项目后台   |     |    |   |   |   |
  
该项目中配置了`HA`机制，以保证项目的连续性解决方案。首先先说明下什么是`HA`。  
　　`HA`意为`High Available`，高可用性集群，是保证项目连续性的有效解决方案，一般有两个或两个以上的节点，且分为活动节点及备用节点。通常把正在执行业务的称为活动节点，而作为活动节点的一个备份的则称为备用节点。当活动节点出现问题，导致正在运行的业务（任务）不能正常运行时，备用节点此时就会侦测到，并立即接续活动节点来执行业务。从而实现业务的不中断或短暂中断。  
　　`Hadoop`的`HA`机制需要`zookeeper`来配合使用。架构图如下  
　　![](./images/frame.jpg)  
**说明：**  
　　在`hadoop2.x`中通常由两个`NameNode`组成，一个处于`active`状态，另一个处于`standby`状态。`Active NameNode`对外提供服务，而`Standby NameNode`则不对外提供服务，仅同步`active namenode`的状态，以便能够在它失败时快速进行切换。  
　　`hadoop2.x`官方提供了两种`HDFS HA`的解决方案，一种是`NFS`，另一种是`QJM`。这里我们使用简单的`QJM`。在该方案中，主备`NameNode`之间通过一组`JournalNode`同步元数据信息，一条数据只要成功写入多数`JournalNode`即认为写入成功。通常配置奇数个`JournalNode`,这里还配置了一个`zookeeper`集群，用于`ZKFC（DFSZKFailoverController）`故障转移，当`Active NameNode`挂掉了，会自动切换`Standby NameNode`为`standby`状态。  
## 整个架构搭建的具体过程  
#### 前期准备工作
前期准备工作需要在每台服务器都要去执行。    
 
1. 修改服务器主机名  
为了不重启系统而是修改主机名生效，并且保证系统重启之后，不会丢失所做的修改，我们使用以下的方式来修改主机名。  
		
		vi ／etc/sysconfig/network   
		//将 HOSTNAME 修改为 Cloud
		....
		NETWORKING=yes 
		NETWORKING_IPV6=yes 
		HOSTNAME=Cloud
然后在执行执行以下命令  

		 // sysctl 是 centos7 以后才有的，请不要在 centos7 版本以下使用
		 sysctl kernel.hostname=Cloud  
执行上述命令之后，在当前终端会话并不会生效，需要重新打开新的会话才会生效。系统重启也不会失效。将上述的两条命令分别在其它的服务器上都执行一遍。  
<font color='red'>注意</font>：需要将主机名更换    
		
		vi ／etc/sysconfig/network	
		HOSTNAME=Cloud2  
		
		//分别也在另外四台服务器上设置
    	//分别为Cloud3、Cloud4、Cloud5、Cloud6
新会话有效  

 		sysctl kernel.hostname=Cloud2 
 		
 		//分别也在另外四台服务器上设置
    	//分别为Cloud3、Cloud4、Cloud5、Cloud6

  
2. 修改`IP`  
具体的网卡名称依服务器上为准，一般是以`ifcfg-`开头,`ifcfg-lo`是`LOOPBACK`网络不用做任何设置。`ip`地址和网关依查到的为准，若是虚拟机安装的话，自行查看自己本地的地址和网关。
  		
  		vi /etc/sysconfig/network-scripts/ifcfg-* 
  		// 加入以下内容或者更新对应的内容
  		BOOTPROTO=static #dhcp改为static（修改）
		ONBOOT=yes #开机启用本配置，一般在最后一行（修改）
		IPADDR=192.168.1.100 #静态IP（增加）
		GATEWAY=192.168.1.1  #默认网关，虚拟机安装的话，通常是2，也就是VMnet8的网关设置（增加）
		NETMASK=255.255.255.0 #子网掩码（增加）
		DNS1=192.168.1.2 #DNS 配置，虚拟机安装的话，DNS就网关就行，多个DNS网址的话再增加（增加）  
使用同样的方法，分别在`Cloud2`、`Cloud3`、`Cloud4`、`Cloud5`、`Cloud6`执行相同的操作。执行完毕后，可以需要重启网络才能生效。使用以下命令重启网络。
		
		 service network restart
重启之后，通过下面的命令查看是否配置成功，并进行验证各个服务器之间是否相同，如果要访问外网也同样需要进行验证，在没有设置`DNS`之前，是不能访问外网的，所以这个时候只需要验证各个服务器之间是否相同即可。

		ip addr # 查看ip地址
		# 在 Cloud 上验证其它是否能与其它机器互通
		ping 192.168.1.102 #在分别与102、103、104、105、106进行验证
3. 修改`DNS`  
修改各个服务器的`DNS`使其能够访问外网，具体修改如下  
		
		vi /etc/resolv.conf
		nameserver 8.8.8.8
		nameserver x.x.x.x # 分配的dns直接加上就可以了
 
4. 修改`hosts`  
`hosts`文件是主机名和`IP`的映射关系，如果设置了`hosts`，可以使用主机名，而不需要在使用对应的冗长的`IP`地址了。具体修改如下,需要在每台服务器上都做如下修改。
		
		vi /etc/hosts
		192.168.1.100 Cloud
		192.168.1.102 Cloud2
		192.168.1.103 Cloud3
		192.168.1.104 Cloud4
		192.168.1.105 Cloud5
		192.168.1.106 Cloud6
		
5. 关闭防火墙  
在关闭之前，需要先查看一下当前的状态，使用一下命令查看，该命令同样是在`centos7`下，`centos7`以下的版本根据对应的版本查看是如何关闭的。

		systemctl status firewalld  
使用下面命令禁用  
		
		systemctl disable firewalld  
		systemctl stop firewalld   
关闭SELinux  
		
		# 临时关闭，不用重启机器  
		setenforce 0  
		# 永久关闭，以防止下次重启生效  
		vi /etc/sysconfig/selinux  
 		# 下面为 selinux 文件中需要修改的元素  
		SELINUX=disabled  
6. 设置`NTP`时间同步  
设置时间同步之前，需要安装`ntp`，执行下面的命令安装，若没有`yum`命令，可以使用`apt-get`命令，在每台机器上都执行以下命令。

		yum -y install ntp	 or  apt-get install ntp
		ntpdate 0.asia.pool.ntp.org # 手动同步
  
7. 配置`ssh`免登录  
先检查一下系统是否已经安装了`ssh`，若没有安装执行对应的命令进行安装，使用下面的命令可以检查是否已经安装。  
		
		ssh # 回车，若提示找不到该命令，使用下一行的命令进行安装，每台服务器都执行相同的步骤
		yum install openssh-server # 或着使用apt-get安装也可以  
		
	为了集群安全，我们需要先创建一个用户，在该用户下配置免密钥登录，而不是在`root`用户下配置免密钥登录，这点请注意，不然的话在`hadoop`用户去启动集群的时候，同样会提示让你输入密码。下面先创建对应的组和用户，<font color='red'>注意：</font>在每台服务上都需要创建。具体如下  

		useradd -g hadoop hadoop # 创建hadoop用户，并且加入hadoop用户组，会自动创建hadoop组
		passwd hadoop # 为hadoop用户创建密码  
	组和用户添加之后，可以切换到`hadoop`用户下面，在`hadoop`用户下执行下面的命令。
	
		su hadoop # 然后输入密码进入
		cd ~ # 下面的两行命令需要在每台服务器都执行
		ssh-keygen -t rsa # 回车会有提示，都按回车就可以
		cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys # 配置服务器本身公钥和密钥
		# 同样下面的命令也需要在每台服务器上执行
		# 实现每台服务器之间的无密钥登录，可以通过手动，也可以通过脚本实现
		# 因为配置过hosts了，所以下面可以直接使用主机名
		# 为了方便我们每台服务器之间都做无密钥登录
		# 配置的时候，提示输入密码
		ssh-copy-id -i ~/.ssh/id_dsa.pub Cloud2(主机名字) # 同样Cloud3及其它机器都要执行

8. 挂载分区和创建应用安装路径  
先在`hadoop`用户根目录下创建`3`个文件夹，分别如下，所有的六台服务器都执行同样的操作。
		
		
		mkdir software			# 300G
		mkdir -p software/app	# 存放安装软件包
		mkdir -p software/src	# 解压之后的软件
		mkdir data				# 集群数据存放 4T
		mkdir data2				# 预留	4T
		mkdir backup			# 备份数据 	  4T
文件夹创建之后，将分区挂载到对应的文件夹下，在挂载之前先将分区格式化一下，同样的每台服务器都需要执行下面的命令  

		mkfs -t ext4 /dev/sdb #具体是sdx，取决于系统，可以通过lsblk查看
		mkfs -t ext4 /dev/sdc
		mkfs -t ext4 /dev/sde
		mkfs -t ext4 /dev/sdf
将对应的分区挂载到刚才创建的四个文夹上，每台服务器都要执行。执行下面的命令  

		mount /dev/sdb software # sdb分区挂载在software目录下
		mount /dev/sdc data		# sdc分区挂载在data目录下
		mount /dev/sde data2	# sde分区挂载在data2目录下
		mount /dev/sdf backup	# sdf分区挂载在backup目录下
为了防止启动丢失信息，这里我们在永久写入一下。执行下面的命令

		vi /etc/fstab  # 在文件后面加入以下内容
		/dev/sdb    /home/hadoop/software    ext4    defaults    0 0
		/dev/sdc    /home/hadoop/data    	 ext4    defaults    0 0
		/dev/sde    /home/hadoop/data2       ext4    defaults    0 0
		/dev/sdf    /home/hadoop/backup      ext4    defaults    0 0
说明：这个文件`1`第列就是`具体的分区`，第`2`列是`分区挂载的目录`，第`3`列是`文件格式`，第`4`列是`挂载规则`，第`5`列是`备份`；`0`为从不备份，或显示上次至今备份之天数；第`7`列是启动时`fsck`检查顺序，`0`为不检查， `“/”`永远为`1`;

9. 环境变量约定  
所有的环境变量设置均在`hadoop`用户下面配置，即在根目录下的`.bashrc`中设置，不需要在`etc/profile`中设置，所以`hadoop`用户不需要任何的`root`权限。 修改环境变量之后，不要忘记重新加载配置文件，不然环境变量不会生效。执行下面的命令生效
	
		source ~/.bashrc  
		
#### 以上就是执行完成以后，我们的准备工作就算完成了，下面可以开始我们的集群的搭建。
## HADOO集群搭建
1. 安装`jdk`  
`jdk`使用`jdk-8u151-linux-x64.tar.gz`，下载好之后，将这个文件移动到`/home/hadoop/software/app`下。在`windwos`上下载的软件，可以通过工具上传到服务器上，上传之后做下面的配置安装。安装配置如下
		
		cd /software/app
		tar -zxvf jdk-8u151-linux-x64.tar.gz
		mv jdk-8u151-linux-x64 ../src
		cd ~
		vi .bashrc # 在文件最后加入jdk的环境变量
		export JAVA_HOME=/home/hadoop/software/src/jdk-8u151-linux-x64
		export PATH=$PATH:$JAVA_HOME/bin
		source ~/.bashrc
		java -version # 验证是否设置成功
将安装好的`jdk`同步到其它的五台服务器上。命令如下
		
		cd /software/src/jdk-8u151-linux-x64
		rm -rf /doc 	# 为了快速的同步，将不要的帮助文档删掉
		cd ..
		scp -r /jdk-8u151-linux-x64 hadoop@Cloud2:/home/hadoop/software/src
		scp -r /jdk-8u151-linux-x64 hadoop@Cloud3:/home/hadoop/software/src
		scp -r /jdk-8u151-linux-x64 hadoop@Cloud4:/home/hadoop/software/src
		scp -r /jdk-8u151-linux-x64 hadoop@Cloud5:/home/hadoop/software/src
		scp -r /jdk-8u151-linux-x64 hadoop@Cloud6:/home/hadoop/software/src
		scp ~/.bashrc hadoop@Cloud2:/home/hadoop/
		scp ~/.bashrc hadoop@Cloud3:/home/hadoop/
		scp ~/.bashrc hadoop@Cloud4:/home/hadoop/
		scp ~/.bashrc hadoop@Cloud5:/home/hadoop/
		scp ~/.bashrc hadoop@Cloud6:/home/hadoop/
到此`jdk`配置完成。
2. 安装配置`zookeeper`集群  
我们使用`zookeeper`版本如下`zookeeper-3.4.8.tar.gz`，下载地址如下  
[点击进入下载页面](https://archive.apache.org/dist/zookeeper/zookeeper-3.5.0-alpha/)，下载之后都样将其移动到`/software/app`这个目录下。具体安装配置如下

		# 执行下面的操作之前，确保已经将文件上传至服务器上
		# 若不是在windows下载的，也可以通过wget命令直接下载到服务器上，但是要确保能访问外网
		cd /software/app
		tar -zxvf zookeeper-3.4.8.tar.gz
		mv zookeeper-3.4.8 ../src
		cd ../src
		cd zookeeper-3.4.8
		mkdir data
		cd conf/
		mv zoo_sample.cfg zoo.cfg
		
		vi zoo.cfg # 修改并添加如下内容
		dataDir=/home/hadoop/software/src/zookeeper-3.4.8/data
		server.4=Cloud4:2888:3888 # 以下内容是需要添加的
		server.5=Cloud5:2888:3888
		server.6=Cloud6:2888:3888
		ESC键 :wq # 保存并退出
		
		cd ../data
		echo "4" > myid
将配置好的`zookeeper`同步`Cloud5`和`Cloud6`服务器上。
		
		cd ~
		scp -r /software/src/zookeeper-3.4.8 hadoop@Cloud5:/home/hadoop/software/src
		scp -r /software/src/zookeeper-3.4.8 hadoop@Cloud6:/home/hadoop/software/src
将`Cloud5`和`Cloud6`上的`zookeeper/data`目录下`myid`文件内容进行更改，这是因为`zookeeper`集群不能重复。
		
		# 在Cloud5上
		cd /software/src/zookeeper-3.4.8/data
		echo "5" > myid 		# > 符号表示写入mydi文件并覆盖原来的内容
		# 在Cloud6上
		cd /software/src/zookeeper-3.4.8/data
		echo "6" > myid
配置完成之后，分别在三台服务器上启动`zookeeper`

		# 在Cloud4上
		cd ~
		cd /software/src/zookeeper-3.4.8a/
		./zkServer.sh start
		# 在Cloud5上
		cd ~
		cd /software/src/zookeeper-3.4.8/
		./zkServer.sh start
		# 在Cloud6上
		cd ~
		cd /software/src/zookeeper-3.4.8/
		./zkServer.sh start
		#分别在Cloud4，Cloud5，Cloud6上执行，下面命令查看是否启动成功。
		./zkServer.sh status
		#若其中两个是follower，一个是leader，则说明成功。
将`zookeeper`添加到环境变量中

		cd ~
		vi .bashrc # 在文件最后加入jdk的环境变量
		export ZK_HOME=/home/hadoop/software/src/zookeeper-3.4.8 		export PATH=$PATH:$ZK_HOME/bin 
		# 同步其它两台配置文件
		scp ~/.bashrc hadoop@Cloud5:/home/hadoop/
		scp ~/.bashrc hadoop@Cloud6:/home/hadoop/

3. 安装配置`hadoop`集群  
使用`hadoop `的这个版本`hadoop-2.8.2.tar.gz `，下载地址如下  
[点击进入下载页面](https://archive.apache.org/dist/hadoop/common/hadoop-2.8.2/)，下载之后都样将其移动到`/software/app`这个目录下。具体安装配置如下 
		
		#解压
		cd /software/app
		tar -zxvf hadoop-2.8.2.tar.gz
		mv hadoop-2.8.2 ../src
		cd ../src
		cd hadoop-2.8.2/etc/hadoop  
*3.1* 修改`hadoop-env.sh`

		export JAVA_HOME=/home/hadoop/software/src/jdk-8u151-linux-x64
*3.2* 修改`core-site.xml`

		<configuration>  
			<!-- 指定hdfs的nameservice为ns1 -->  
			<property>  
				<name>fs.defaultFS</name>  
				<value>hdfs://ns1</value>  
			</property>  
			<!-- 指定hadoop临时目录 -->  
			<property>  
				<name>hadoop.tmp.dir</name>  
				<value>/home/hadoop/software/src/hadoop-2.8.2/tmp</value>  
			</property>  
			<!-- 指定zookeeper地址 -->  
			<property>  
				<name>ha.zookeeper.quorum</name>  
				<value>Cloud4:2181, Cloud5:2181, Cloud6:2181</value>  
			</property>  
		</configuration>   
*3.3* 修改`hdfs-site.xml`
	
		<configuration>  
			<!--指定hdfs的nameservice为ns1，需要和core-site.xml中的保持一致 -->  
			<property>  
				<name>dfs.nameservices</name>  
				<value>ns1</value>  
			</property>  
			<!-- ns1下面有两个NameNode，分别是nn1，nn2 -->  
			<property>  
				<name>dfs.ha.namenodes.ns1</name>  
				<value>nn1,nn2</value>  
			</property>  
			<!-- nn1的RPC通信地址 -->  
			<property>  
				<name>dfs.namenode.rpc-address.ns1.nn1</name>  
				<value>Cloud:9000</value>  
			</property>  
			<!-- nn1的http通信地址 -->  
			<property>  
				<name>dfs.namenode.http-address.ns1.nn1</name>  
				<value>Cloud:50070</value>  
			</property>  
			<!-- nn2的RPC通信地址 -->  
			<property>  
				<name>dfs.namenode.rpc-address.ns1.nn2</name>  
				<value>Cloud2:9000</value>  
			</property>  
			<!-- nn2的http通信地址 -->  
			<property>  
				<name>dfs.namenode.http-address.ns1.nn2</name>  
				<value> Cloud2:50070</value>  
			</property>  
			<!-- 指定NameNode的元数据在JournalNode上的存放位置 -->  
			<property>  
				<name>dfs.namenode.shared.edits.dir</name>  
				<value>qjournal://Cloud4:8485; Cloud5:8485; Cloud6:8485/ns1</value>  
			</property>  
			<!-- 指定JournalNode在本地磁盘存放数据的位置 -->  
			<property>  
				<name>dfs.journalnode.edits.dir</name>  
				<value>/home/hadoop/software/src/hadoop-2.8.2/journal</value>  
			</property>  
			<!-- 开启NameNode失败自动切换 -->  
			<property>  
				<name>dfs.ha.automatic-failover.enabled</name>  
				<value>true</value>  
			</property>  
			<!-- 配置失败自动切换实现方式 -->  
			<property>  
				<name>dfs.client.failover.proxy.provider.ns1</name>  
			<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>  
			</property>  
			<!-- 配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行-->  
			<property>  
				<name>dfs.ha.fencing.methods</name>  
				<value>  
				sshfence  
				shell(/bin/true)  
				</value>  
			</property>  
			<!-- 使用sshfence隔离机制时需要ssh免登陆 -->  
			<property>  
				<name>dfs.ha.fencing.ssh.private-key-files</name>  
				<value>/home/hadoop/.ssh/id_rsa</value>  
			</property>  
			<!-- 配置sshfence隔离机制超时时间 -->  
			<property>  
				<name>dfs.ha.fencing.ssh.connect-timeout</name>  
				<value>30000</value>  
			</property>  
		</configuration>  
*3.4* 修改`mapred-site.xml`
		
		<configuration>  
		<!-- 指定mr框架为yarn方式 -->  
			<property>  
				<name>mapreduce.framework.name</name>  
				<value>yarn</value>  
			</property>  
		</configuration>  
*3.5* 修改`yarn-site.xml`  
		
		<configuration>  
			<!-- 指定resourcemanager地址 -->  
			<property>  
				<name>yarn.resourcemanager.hostname</name>  
				<value>Cloud3</value>  
			</property>  
			<!-- 指定nodemanager启动时加载server的方式为shuffle server -->  
			<property>  
				<name>yarn.nodemanager.aux-services</name>  
				<value>mapreduce_shuffle</value>  
			</property>  
		</configuration>  
		
*3.6*  创建对应的文件夹  
		
		cd hadoop-2.8.2/
		mkdir tmp
		mkdir journal
		
*3.7* 修改`slaves`文件  
`slaves`是指定子节点的位置，因为要在`Cloud`上启动`HDFS`、在`Cloud3`启动`yarn`，所以`Cloud`上的`slaves`文件指定的是`datanode`的位置，`Cloud3`上的`slaves`文件指定的是`nodemanager`的位置
	
		vi slaves
		Cloud4
		Cloud5
		Cloud6
*3.8* 将配置好的`hadoop`拷贝到其它节点上
		
		cd ~
		cd /software/src/
		scp -r hadoop-2.8.2/ hadoop@Cloud2:/home/hadoop/software/src
		scp -r hadoop-2.8.2/ hadoop@Cloud3:/home/hadoop/software/src
		scp -r hadoop-2.8.2/ hadoop@Cloud4:/home/hadoop/software/src
		scp -r hadoop-2.8.2/ hadoop@Cloud5:/home/hadoop/software/src
		scp -r hadoop-2.8.2/ hadoop@Cloud6:/home/hadoop/software/src
		
	*3.8* 配置环境变量

		cd ~
		vi .bashrc
		#添加如下内容
		export HADOOP_HOME=/home/hadoop/software/src/hadoop-2.8.2
		export PATH=$PATH:$HADOOP_HOME/bin
		#在其它五台节点上也执行同样的操作，因为前面配置zookeeper环境变量了
		#所以这里不能在直接拷贝过去，需要手动在每台节点上添加环境变量

4.  验证`hadoop`和`zookeeper`  

	*4.1* 启动`zookeeper`
若之前启动的`zookeeper`进程还活着，并且正常运行，可以直接进行下面的操作，若服务不正常，可以将其关掉并重新启动。  

	*4.2* 启动`journalnode`   
在`Cloud`上启动所有`journalnode`<font color='red'>注意：</font>是调用的`hadoop-daemons.sh`这个脚本，注意是复数s的那个脚本。

		cd /software/src/hadoop-2.8.2
		sbin/hadoop-daemons.sh start journalnode
		#在Cloud4，Cloud5，Cloud6上运行jps命令查看是否启动
		#若存在JournalNode进程，即成功  
	*4.3* 格式化`HDFS`  
	在`Cloud`上执行下面命令
			
		hdfs namenode -format  
格式化后会在根据`core-site.xml`中的`hadoop.tmp.dir`配置生成个文件，这里我配置的是`/home/hadoop/software/src/hadoop-2.8.2/tmp`，然后将`/home/hadoop/software/src/hadoop-2.8.2/tmp`拷贝到`Cloud2`的`/home/hadoop/software/src/hadoop-2.8.2/`下。

		scp -r hadoop-2.8.2/tmp/ hadoop@Cloud2:/home/hadoop/software/src/hadoop-2.8.2/
	
	*4.4* 格式化`zookeeper`  
		
		hdfs zkfc -formatZK
	*4.5* 启动`HDFS`（在`Cloud`上）  
		
		sbin/start-dfs.sh
		
	*4.6* 启动`YARN`  
	<font color='red'>注意：</font>：是在`Cloud3`上执行`start-yarn.sh`，把`namenode`和`resourcemanager`分开是因为性能问题，因为他们都要占用大量资源，所以把他们分开了，他们分开了就要分别在不同的机器上启动。  
	
		sbin/start-yarn.sh
		
#### 到此`hadoop`集群配置完毕，可以对其测试。  
1. 验证`hdfs HA`机制  
验证上传文件成功之后，手动杀掉一个`namenode`，在查看该文件看是否存在。  

2.  验证`YARN`  
运行一下`hadoop`提供的`demo`中的`WordCount`程序

		hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar wordcount /xxx /xxx

## Hbase集群搭建
1. `Hbase`使用`hbase-1.2.6-bin.tar.gz `，[下载地址传送门](http://mirrors.shu.edu.cn/apache/hbase/1.2.6/)。下载好之后，将这个文件移动到`/home/hadoop/software/app`下。在`windwos`上下载的软件，可以通过工具上传到服务器上，上传之后做下面的配置安装。安装配置如下

	*1.2*　修改`hbase-env.sh`文件  

		cd ~ 
		cd /software/app/
		tar -zxvf hbase-1.2.6-bin.tar.gz
		mv hbase-1.2.6 ../src
		cd ../src/hbase-1.2.6
		#修改配置文件
		vi hbase-env.sh
		export JAVA_HOME=/home/hadoop/software/src/jdk-8u151-linux-x64  # 如果jdk路径不同需要单独配置
		export HBASE_CLASSPATH=/home/hadoop/software/src/hadoop-2.8.2/etc/hadoop/
		export HBASE_MANAGES_ZK=false	# 不使用hbase自带的zk
	*1.3*　修改`hbase-site.xml`
	
		<configuration>
	        <property>
	                <name>hbase.rootdir</name>
	                <value>hdfs://Cloud:9000/hbase</value>
	        </property>
			<property>
		        <name>hbase.master</name>
		        <value>Cloud</value>
		    </property>
	        <property>
	                <name>hbase.zookeeper.property.clientPort</name>
	                <value>2181</value>
	        </property>
	        <property>
	                <name>zookeeper.session.timeout</name>
	                <value>120000</value>
	        </property>
	        <property>
	                <name>hbase.zookeeper.quorum</name>
	                <value>Cloud4,Cloud5,Cloud6</value>
	        </property>
	        <property>
	                <name>hbase.tmp.dir</name>
	                <value>/home/hadoop/software/src/hbase-1.2.6/hbasedata</value>
	        </property>
	        <property>
	                <name>hbase.cluster.distributed</name>
	                <value>true</value>
	        </property>
		</configuration>		
	*1.4* 创建对应的文件夹
		
		cd hbase-1.2.6/
		mkdir hbasedata
		
	*1.5* 修改`regionservers`文件
	
		Cloud2
		Cloud3
	*1.6* 修改环境变量   
	 		
	 		cd ~
	 		vi .bashrc
	 		export HBASE_HOME=/hoem/hadoop/software/src/hbase-1.2.6/
	 		export PATH=$PATH:$HBASE_HOME/bin
	 		#将上面的环境变量同样在Cloud2、Cloud3上执行。
	 	
	*1.7* 将`hbase`拷贝到`Cloud2`，`Cloud3`节点上
		
		cd /software/src
		scp -r hbase-1.2.6 hadoop@Cloud2:/home/hadoop/software/src
		scp -r hbase-1.2.6 hadoop@Cloud3:/home/hadoop/software/src　　　　　　　　　
	
	*1.8* 启动`hbase`  
	在启动`hbase`之前，请确保`zookeeper`和`hadoop`都已经启动，并且正常。
	
		#在Cloud上执行
		cd software/src/hbase-1.2.6
		bin/start-hbase.sh
		#启动完成后，分别在Cloud、Cloud2、Cloud3上执行jps命令
		#Cloud上会多出一个HMaster进程
		#Cloud2上会多出一个HRegionServer进程
		#Cloud3上会多出一个HRegionServer进程
		
## Spark集群搭建
1. `Spark `使用`spark-2.2.0-bin-hadoop2.7.tgz`，[下载地址传送门](https://www.apache.org/dyn/closer.lua/spark/spark-2.2.0/spark-2.2.0-bin-hadoop2.7.tgz)。下载好之后，将这个文件移动到`/home/hadoop/software/app`下。在`windwos`上下载的软件，可以通过工具上传到服务器上，上传之后做下面的配置安装。

2. 因为`Spark`需要使用`scala`，所以我们先安装`scala`。[下载地址传送门](https://downloads.lightbend.com/scala/2.12.4/scala-2.12.4.tgz)，下载之后，移动到`software/app/`目录下。

		cd　software/app
		tar -zxvf scala-2.12.4.tgz
		tar -zxvf spark-2.2.0-bin-hadoop2.7.tgz
		mv scala-2.12.4 ../src
		mv spark-2.2.0-bin-hadoop2.7 ../src
	*2.1* 配置环境变量
	
		cd ~ 
		vi .bashrc
		export SCALA_HOME=/home/hadoop/software/src/scala-2.12.4
		export PATH=$PATH:$SCALA_HOME/bin 
		export SPARK_HOME=/home/hadoop/software/src/spark-2.2.0-bin-hadoop2.7
		export PATH=$PATH:$SPARK_HOME/bin
		# 将上面的环境变量在Cloud2和Cloud3上同样添加
3. 配置`Spark`  
	*3.1* 修改`spark-env.sh`
		
		cd software/src/spark-2.2.0-bin-hadoop2.7/conf
		cp spark-env.sh.template spark-env.sh 
		# 在末尾加入
		vi spark-env.sh
		export JAVA_HOME=/home/hadoop/software/src/jdk-8u151-linux-x64
		export SCALA_HOME=/home/hadoop/bigdata/src/scala-2.12.4
		export SPARK_WORKER_MEMORY=2g
		export SPARK_MASTER_IP=192.168.1.100 #Master对应的ip
		export HADOOP_CONF_DIR=/home/hadoop/software/src/hadoop-2.8.2
		export SPARK_JAR=/home/hadoop/software/src/spark-2.2.0-bin-hadoop2.7/jars
		
	*3.2* 修改`slaves`文件
	
		cd software/src/spark-2.2.0-bin-hadoop2.7/conf
		cp slaves.template slaves 
		# 添加work节点
		vi slaves
		Cloud2
		Cloud3
		
	*3.3*	`spark`集群复制  
	
		cd ~
		cd software/src/
		scp -r scala-2.12.4/ hadoop@Cloud2:/home/hadoop/software/src/
		scp -r scala-2.12.4/ hadoop@Cloud3:/home/hadoop/software/src/
		scp -r spark-2.2.0-bin-hadoop2.7/ hadoop@Cloud2:/home/hadoop/software/src/
		scp -r spark-2.2.0-bin-hadoop2.7/ hadoop@Cloud2:/home/hadoop/software/src/
	*3.4* `spark`集群启动
	
		cd software/src/spark-2.2.0-bin-hadoop2.7
		sbin/start-all.sh
		#在Cloud、Cloud2、Cloud3上使用jps命令查看是否配置成功
		#Cloud上会多出一个Master进程
		#Cloud2和Cloud3上会多出一个Worker进程
		
## hive集群搭建
1. `Hive `使用`apache-hive-2.2.0-bin.tar.gz`，[下载地址传送门](http://mirrors.hust.edu.cn/apache/hive/hive-2.2.0/)。下载好之后，将这个文件移动到`/home/hadoop/software/app`下。具体配置如下
	
		cd ~
		cd software/app
		tar -zxvf apache-hive-2.2.0-bin.tar.gz
		mv apache-hive-2.2.0-bin ../src
		#配置环境变量
		cd ~
		vi .bashrc
		export HIVE_HOME=/home/hadoop/software/src/apache-hive-2.2.0-bin
		export PATH=$PATH:$HIVE_HOME/bin
		#最后
		source .bashrc
2. 对`hive`进行配置  
	
		cd ~
		cd software/src/apache-hive-2.2.0-bin/conf
		cp hive-env.sh.template hive-env.sh
		#加入以下内容
		vi hive-env.sh
		export JAVA_HOME=/home/hadoop/software/src/jdk-8u151-linux-x64
		export HADOOP_HOME=/home/hadoop/software/src/hadoop-2.8.2
		export HIVE_HOME=/home/hadoop/bigdata/src/apache-hive-2.2.0-bin
		export HIVE_CONF_DIR=/home/hadoop/bigdata/src/apache-hive-2.2.0-bin/conf
3. 修改`hbase-site.xml`文件
		
		cd software/src/apache-hive-2.2.0-bin/conf
		touch hive-site.xml
		vi hive-site.xml
		<configuration>
		    <property>
		        <name>javax.jdo.option.ConnectionURL</name>
		        <value>jdbc:mysql://hive:3306/hive?createDatabaseIfNotExist=true</value>
		    </property>   
		    <property> 
		        <name>javax.jdo.option.ConnectionDriverName</name> 
		        <value>com.mysql.jdbc.Driver</value>      
		    </property>               
		
		    <property> 
		        <name>javax.jdo.option.ConnectionUserName</name>
		        <value>hive<value>
		    </property>
		    <property>  
		        <name>javax.jdo.option.ConnectionPassword</name>
		        <value>hive</value>
		    </property>          
		</configuration>
4. `jdbc`驱动下载

		wget http://cdn.mysql.com/Downloads/Connector-J/mysql-connector-java-5.1.36.tar.gz
		cp mysql-connector-java-5.1.33-bin.jar apache-hive-2.2.0-bin/lib/
		

