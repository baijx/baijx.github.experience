# 安装redis

## 下载压缩包
> http://download.redis.io/releases/

## 解压压缩包
```
# tar -zxvf redis-3.2.10.tar.gz
```

## 移动文件
```
# mv redis-3.2.10 /usr/local/redis
```

## 安装gcc依赖
```
# yum -y install gcc
```

## 编译安装
```
# cd /usr/local/redis/redis-3.2.10/
# make MALLOC=libc
# cd src && make install
    CC Makefile.dep

Hint: It's a good idea to run 'make test' ;)

    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
```

## 启动redis的三种方式

### 1. 直接启动redis
> 不推荐，不能关闭窗口，但是可以作为测试用。能启动后Ctrl+C关闭，跳转到redis.conf配置章节
```
# cd /usr/local/redis/redis-3.2.10/src
# ./redis-server
...
```

### 2. 以后台进程方式启动redis
> 修改conf文件，按conf文件启动
```
# vim /usr/local/redis/redis-3.2.10/redis.conf
将daemonize no改为daemonize yes
# ./redis-server /usr/local/redis/redis-3.2.10/redis.conf
# ps -ef|grep redis
...
# kill -9 pid
```

## 3. 设置redis开机自启动
> 将redis注册为一个服务，开机自启。最好再redis.conf配置好后再进行此操作
```
# cd /etc
# mkdir redis
# cp /usr/local/redis/redis-3.2.10/redis.conf /etc/redis/6379.conf
注：6379.conf是对应启动脚本redis_init_script中的CONF="/etc/redis/${REDISPORT}.conf"，不是随便更改的
# cp /usr/local/redis/redis-3.2.10/utils/redis_init_script /etc/init.d/redis
# chkconfig redis on
service redis does not support chkconfig
注：redis不支持chkconfig，解决方法：
使用vim编辑redis文件，在第一行下加入如下两行注释，保存退出
# chkconfig:   2345 90 10
# description:  Redis is a persistent key-value database
注释的意思是，redis服务必须在运行级2，3，4，5下被启动或关闭，启动的优先级是90，关闭的优先级是10。
# chkconfig redis on

检查是否添加成功
# chkconfig --list redis

启动
# systemctl start redis
停止
# systemctl stop redis
查看状态
# systemctl status redis

注：如果出现一下问题，则重启服务器
# service redisd start
/var/run/redis_6379.pid exists, process is already running or crashed
```

## 开启远程连接
> 默认情况下，redis无法远程连接，需要修改配置

### 1. 修改redis.conf
* bind 127.0.0.1 注释掉或者修改为bind 0.0.0.0
* protected-mode yes 改为 protected-mode no
* 如果需要密码，取消注释requirepass foobared，并将foobared修改为复杂的密码

### 2. 防火墙开放端口6379
```
# firewall-cmd --zone=public --add-port=6379/tcp --permanent
# firewall-cmd --reload
```

