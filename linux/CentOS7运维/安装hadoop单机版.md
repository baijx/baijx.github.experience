s# 安装hadoop单机版

## 一、Linux系统设置

### 设置静态IP（略）
远程访问时需要，客户端hosts也要加上
# vim /etc/hosts

在hosts文件的最下面一行添加，格式为：

第一部份：网络IP地址。

第二部份：主机名.域名，注意主机名和域名之间有个半角的点。

第二部份：主机名(主机名别名） ，其实就是主机名。

比如：192.168.1.109 hadoop-master.localdomain hadoop-master

当然每行也可以是两部份，就是主机IP地址和主机名；

比如：192.168.1.109 hadoop-master

### 设置主机名与 IP 地址映射（可选）
```
# vim /etc/hostname --修改主机名（如，删掉原有内容，命名为 hadoop）
# systemctl restart network --重启网卡
# ping hadoop --ping 通证明成功
```

### 免密码 ssh 设置
> 如果不配置启动hadoop时会denied
```
# ssh-keygen -t rsa --然后一路回车即可
# cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys --复制到公共密钥中
# ssh localhost --测试
```

## 二、安装java8（略）

## 三、安装hadoop3.2.1

### 下载
```
# wget http://mirrors.hust.edu.cn/apache/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
```

### 解压
```
# mkdir /opt/hadoop
# tar -xzvf hadoop-3.2.1.tar.gz -C /opt/hadoop/
```

### 设置环境变量
```
# vim /etc/profile

export HADOOP_HOME=/opt/hadoop/hadoop-3.2.1
export PATH=$PATH:$HADOOP_HOME/bin

# source /etc/profile
# hadoop version
Hadoop 3.2.1
```

### 创建文件夹
```
# mkdir /opt/hadoop/tmp
# mkdir /opt/hadoop/hdfs
# mkdir /opt/hadoop/hdfs/data
# mkdir /opt/hadoop/hdfs/name
```

### 修改 Hadoop 配置文件
```
# cd /opt/hadoop/hadoop-3.2.1/etc/hadoop/
```

#### 1) hadoop-env.sh
> 设置JAVA_HOM和USER
```
# vim hadoop-env.sh
export JAVA_HOME=/opt/java/jdk1.8.0_181

export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
```
#### 2) core-site.xml
```
# vim core-site.xml

<configuration>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>/usr/local/hadoop/tmp</value>
    <description>namenode上本地的hadoop临时文件夹</description>
  </property>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://192.168.105.122:9000</value>
    <description>HDFS的URI，文件系统://namenode标识:端口号</description>
  </property>
</configuration>
```
#### 3) hdfs-site.xml
```
# vim hdfs-site.xml

<configuration>
 <property>
    <name>dfs.replication</name>
    <value>1</value>
    <description>副本个数，配置默认是3,应小于datanode机器数量</description>
 </property>

  <property>
    <name>dfs.name.dir</name>
    <value>/opt/hadoop/hdfs/name</value>
  </property>

  <property>
    <name>dfs.data.dir</name>
    <value>/opt/hadoop/hdfs/data</value>
  </property>

  <property>
    <name>dfs.namenode.http-address</name>
    <value>http://192.168.105.122:9870</value>
    <description>0.0.0.0:9870</description>
  </property>
    
  <property>
    <name>dfs.webhdfs.enabled</name>
    <value>true</value>
    <description>WEBHDFS配置</description>
  </property>
</configuration>
```

#### 4) mapred-site.xml
```
# vim mapred-site.xml

<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
    <description>通知框架MR使用YARN</description>
  </property>
</configuration>
```

### 5) yarn-site.xml
```
# vim yarn-site.xml

<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
    <description>通知框架MR使用YARN</description>
  </property>
</configuration>
```

### 启动和测试

#### 1) 格式化 namenode
```
# cd /opt/hadoop/hadoop-3.2.1/bin
# ./hdfs namenode -format
等几秒没报错就是执行成功，格式化成功后，可以在看到在/opt/hadoop/hdfs/name/目录多了一个current目录，而且该目录内有一系列文件。
# ls /opt/hadoop/hdfs/name/current/
fsimage_0000000000000000000  fsimage_0000000000000000000.md5  seen_txid  VERSION
```

#### 2) 启动hadoop
```
# cd /opt/hadoop/hadoop-3.2.1/sbin
# ./start-all.sh
Starting namenodes on [192.168.105.122]
上一次登录：四 6月 18 11:18:07 CST 2020从 localhostpts/3 上
Starting datanodes
上一次登录：四 6月 18 11:18:27 CST 2020pts/0 上
Starting secondary namenodes [localhost]
上一次登录：四 6月 18 11:18:30 CST 2020pts/0 上
Starting resourcemanager
上一次登录：四 6月 18 11:18:39 CST 2020pts/0 上
Starting nodemanagers
上一次登录：四 6月 18 11:18:54 CST 2020pts/0 上
# 
```

#### 3) 验证
```
# jps
5824 NameNode
1153 QuorumPeerMain
5954 DataNode
6162 SecondaryNameNode
6553 NodeManager
7483 Jps
6430 ResourceManager
```

#### 4) 管理页面
```
Namenode information: http://hadoop1:9870
All Applications: http://hadoop1:8088
HDFS NameNode web interface: http://hadoop1:8042
```

### 开机启动
```
# cd /etc/init.d
# vim hadoop

#!/bin/bash
#chkconfig:2345 20 90
#description:hadoop
#processname:hadoop
export JAVA_HOME=/opt/java/jdk1.8.0_181
case $1 in
    start) su root /opt/hadoop/hadoop-3.2.1/sbin/start-all.sh;;
    stop) su root /opt/hadoop/hadoop-3.2.1/sbin/sbin/stop-all.sh;;
    *) echo "require start|stop" ;;
esac

:ws

chmod +x hadoop  --可执行
chkconfig --add hadoop  --开机启动
chkconfig --list --查看

systemctl start/stop/status hadoop --验证服务
```

### 开放端口
```
# firewall-cmd --zone=public --add-port=9000/tcp --permanent
# firewall-cmd --zone=public --add-port=9870/tcp --permanent
# firewall-cmd --zone=public --add-port=8088/tcp --permanent
# firewall-cmd --zone=public --add-port=8042/tcp --permanent
# firewall-cmd --zone=public --add-port=9864/tcp --permanent
# firewall-cmd --zone=public --add-port=9865/tcp --permanent
# firewall-cmd --zone=public --add-port=9866/tcp --permanent
# firewall-cmd --reload
```