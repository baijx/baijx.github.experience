# 安装mysql8

---
## 删除mysql
### 查看是否有安装过mysql
```
# rpm -qa | grep -i mysql
mysql-community-client-5.7.22-1.el7.x86_64
mysql-community-common-5.7.22-1.el7.x86_64
mysql-community-libs-5.7.22-1.el7.x86_64
mysql-community-libs-compat-5.7.22-1.el7.x86_64
mysql-community-server-5.7.22-1.el7.x86_64
```

### 关闭mysql
> 查看MySQL服务是否启动，如状态是启动的，请执行关闭命令
```
# systemctl status mysql
...
...
# systemctl stop mysql

```

### 删除mysql
```
# yum -y remove MySQL-*
```

### 把所有出现的目录统统删除

```
# find / -name mysql
...
...
# rm -rf 路径

```

### 删除配置文件
```
# rm -rf /etc/my.cnf
```

### 删除mysql的默认密码
```
# rm -rf /root/.mysql_sercret
```
---

## 安装mysql
> 这里介绍压缩包解压方式安装。yum安装方式参考https://blog.csdn.net/our_times/article/details/98882701

### 解压压缩包，移动到指定位置
```
# tar -Jxf mysql-8.0.20-linux-glibc2.12-x86_64.tar.xz
# mv mysql-8.0.20-linux-glibc2.12-x86_64 /usr/local/mysql
```

## 修改my.cnf配置文件
```
# vim /etc/my.cnf
[mysqld]
server_id=1
port=3306
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
log-error=/var/log/mysqld.log
pid-file=/tmp/mysqld/mysqld.pid

transaction_isolation=READ-COMMITTED
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci
init_connect='SET NAMES utf8mb4'
lower_case_table_names=1
max_connections=200
[client]
port=3306
socket=/tmp/mysql.sock
```

## 创建mysql组和mysql用户
```
# groupadd mysql
# useradd -r -g mysql mysql
# chown -R mysql:mysql /usr/local/mysql
```

## 文件权限处理
```
# chown -R mysql:mysql /usr/local/mysql

# cd /tmp
# touch mysql.sock
# chown mysql:mysql mysql.sock

# cd /tmp
# mkdir mysqld
# cd mysqld
# touch mysqld.pid
# chmod 755 mysqld.pid
# cd ..
# chown -R mysql:mysql mysqld

# touch /var/log/mysqld.log
# chown -R mysql:mysql /var/log
# cd /var/log/
# chmod 755 mysqld.log
```

## 初始化和启动数据库
```
# cd /usr/local/mysql/
# ./bin/mysqld --initialize --user=mysql
注：如果出现一下错误，需要yum install -y libaio
./mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory

# ./support-files/mysql.server start
注：是否启动成功可以ps -ef|grep mysql查看
```

## 获取和记录密码
```
# cat /var/log/mysqld.log
```

## 修改密码
```
# cd /usr/local/mysql/bin/
# ./mysql -u root -p
> ALTER USER USER() IDENTIFIED BY 'root';
> FLUSH PRIVILEGES; --立即生效
> quit; --退出
```

## 设置开机自启动
```
# cp -a /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
# chkconfig --add mysql
# chkconfig --list mysql --查看
启动
# systemctl start mysql
停止
# systemctl stop mysql
查看
# systemctl status mysql
```

## 创建快捷方式
> 服务启动后，直接运行mysql -u root -p即可登录，不需要进入到对应的目录
```
# ln -s /usr/local/mysql/bin/mysql /usr/bin
```

### 防火墙开放端口3306
```
# firewall-cmd --zone=public --add-port=3306/tcp --permanent
# firewall-cmd --reload
```

---
# 使用mysql

## 用户操作
```
> CREATE USER 'deepin'@'%' IDENTIFIED BY 'deepin'; --创建用户，%代表可以远程的用户
> ALTER USER 'deepin'@'%' IDENTIFIED WITH MYSQL_NATIVE_PASSWORD BY 'deepin'; --改密
> GRANT SELECT, DELETE, UPDATE, CREATE, DROP ON *.* to 'deepin'@'%'; --授权1
> GRANT ALL ON xxdb.* TO 'deepin'@'%'; --授权2
> GRANT ALL ON *.* to 'deepin'@'%'; --授权2
> FLUSH PRIVILEGES; --立即生效

> select host, user, authentication_string, plugin from user; -- 查看现有用户
默认情况下root用户的host是localhost，即不可以远程访问，会报错Client does not support authentication protocol requested  by server。如果需要远程，可以修改为%
> update user set host = '%' where user = 'root'; 
> FLUSH PRIVILEGES; --立即生效
```

