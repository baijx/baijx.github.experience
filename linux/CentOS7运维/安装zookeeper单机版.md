# 安装zookeeper单机版

## 安装java
> 上传并解压到/opt/java下
```
# mkdir /opt/java
# tar -xzvf jdk-8u181-linux-x64.tar.gz -C /opt/java/
```
> 设置环境变量
```
# vim /etc/environment 
JAVA_HOME=/opt/java/jdk1.8.0_181
JRE_HOME=/opt/java/jdk1.8.0_181/jre

# /etc/profile
...
export JAVA_HOME=/opt/java/jdk1.8.0_181
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin

# source /etc/profile  #使配置文件生效
# java -version # 测试
```

## 安装zk
> 从 https://zookeeper.apache.org/releases.html 下载所需版本，解压
```
# tar -xzvf apache-zookeeper-3.6.1-bin.tar.gz
# mv apache-zookeeper-3.6.1-bin /usr/local/apache-zookeeper-3.6.1
```
> 创建目录存储data
```
# midkr /usr/local/apache-zookeeper-3.6.1/data
```
> 修改配置文件
```
# cd /usr/local/apache-zookeeper-3.6.1/conf
# cp zoo_sample.cfg zoo.cfg
# vim zoo.cfg
dataDir=/usr/local/apache-zookeeper-3.6.1/data
clientPort=2181 # 默认端口
```

## 启动zk
> 启动
```
# cd /usr/local/apache-zookeeper-3.6.1/bin
# ]# ./zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/apache-zookeeper-3.6.1/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```
> 查看状态
```
# ./zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /usr/local/apache-zookeeper-3.6.1/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost.
Mode: standalone
```

### 防火墙开放端口2181
```
# firewall-cmd --zone=public --add-port=2181/tcp --permanent
# firewall-cmd --reload
```

## 开机启动
> 关闭正在运行的zookeeper
```
# ./zkServer.sh stop
ZooKeeper JMX enabled by default
Using config: /usr/local/apache-zookeeper-3.6.1/bin/../conf/zoo.cfg
Stopping zookeeper ... STOPPED
```
> 创建启动服务脚本，注意路径
```
# cd /etc/init.d
# vim zookeeper
#!/bin/bash
#chkconfig:2345 20 90
#description:zookeeper
#processname:zookeeper
ZK_PATH=/usr/local/apache-zookeeper-3.6.1
export JAVA_HOME=/opt/java/jdk1.8.0_181
case $1 in
         start) sh  $ZK_PATH/bin/zkServer.sh start;;
         stop)  sh  $ZK_PATH/bin/zkServer.sh stop;;
         status) sh  $ZK_PATH/bin/zkServer.sh status;;
         restart) sh $ZK_PATH/bin/zkServer.sh restart;;
         *)  echo "require start|stop|status|restart"  ;;
esac
```
> 添加执行权限
```
# chmod ugo+x zookeeper
```
> 注册为服务
```
# chkconfig --add zookeeper
# chkconfig --list zookeeper 查看
```
> 启动、停止、查看
```
启动
# systemctl start zookeeper
停止
# systemctl stop zookeeper
查看
# systemctl status zookeeper
```