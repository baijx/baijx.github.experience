# 安装pgsql12

## 更新源
> 访问官网：[https://yum.postgresql.org/repopackages.php](https://yum.postgresql.org/repopackages.php)，右击CentOS 7 - x86_64复制链接地址获取最新板rmp源，安装
```
# yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

## 安装
> 查看postgresql源，选择要安装的版本
```
# yum list | grep postgresql
```
> 安装服务
```
yum install -y postgresql12-server.x86_64
```
> 安装客户端(可选，不推荐)
```
yum install -y postgresql12.x86_64
```

## 初始化数据库
- 安装目录：/usr/pgsql-version
- 数据目录：/var/lib/pgsql/12/data

> 如果var空间不够，需要修改默认的数据目录则执行以下操作，否则略过
```
# vim /usr/lib/systemd/system/postgresql-12.service
找到Environment=PGDATA=/var/lib/pgsql/12/data/，将当前数据保存位置更新成自己指定位置
```
> 执行初始化
```
# /usr/pgsql-12/bin/postgresql-12-setup initdb
Initializing database ... OK
```
## 启动以及开机自启
```
# systemctl start postgresql-12
# systemctl enable postgresql-12
```
## 登录postgresql，数据库操作
> postgresql在安装时默认添加用户postgres。添加密码，注意语句末尾要加分号
```
# su - postgres --切换
-bash-4.2$ psql --进入psql
psql (12.3)
输入 "help" 来获取帮助信息.

postgres=# ALTER USER postgres WITH PASSWORD 'postgres'; --修改密码
ALTER ROLE
```
> 创建数据库
```
postgres=# CREATE DATABASE devdb;
CREATE DATABASE
```
> 创建用户
```
postgres=# CREATE USER devuser CREATEDB LOGIN PASSWORD 'devuser';
CREATE ROLE
```
> 数据库赋权用户
```
postgres=# GRANT ALL ON DATABASE dolphinscheduler TO devuser; 
GRANT
```
> 查看用户
```
postgres=# \du
                             角色列表
 角色名称 |                    属性                    | 成员属于 
----------+--------------------------------------------+----------
 devuser  | 建立 DB                                    | {}
 postgres | 超级用户, 建立角色, 建立 DB, 复制, 绕过RLS | {}
```
> 查看数据库
```
postgres=# \l
                                         数据库列表
       名称       |  拥有者  | 字元编码 |  校对规则   |    Ctype    |       存取权限        
------------------+----------+----------+-------------+-------------+-----------------------
 devdb            | postgres | UTF8     | zh_CN.UTF-8 | zh_CN.UTF-8 | =Tc/postgres         +
                  |          |          |             |             | postgres=CTc/postgres+
                  |          |          |             |             | devuser=CTc/postgres
 postgres         | postgres | UTF8     | zh_CN.UTF-8 | zh_CN.UTF-8 | 
 template0        | postgres | UTF8     | zh_CN.UTF-8 | zh_CN.UTF-8 | =c/postgres          +
                  |          |          |             |             | postgres=CTc/postgres
 template1        | postgres | UTF8     | zh_CN.UTF-8 | zh_CN.UTF-8 | =c/postgres          +
                  |          |          |             |             | postgres=CTc/postgres
(4 行记录)
```
> 删除数据库
```
postgres=# DROP DATABASE devdb;
```
> 删除用户
```
postgres=# DROP ROLE devuser;
```
> 退出
```
postgres=# \q --退出
```

## 设置远程登录
> 修改远程配置信息
```
# cd /var/lib/pgsql/12/data/
# vim pg_hba.conf
# 追加以下信息
# self config
host    all             all             0.0.0.0/0               md5
# 修改监听，
# vim postgresql.conf
listen_addresses = '*         '         # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
                                        # (change requires restart)
port = 5432                             # (change requires restart)
max_connections = 100                   # (change requires restart)
```
> 重启
```
# systemctl restart postgresql-12
```
> 防火墙开放5432端口
```
# firewall-cmd --zone=public --add-port=5432/tcp --permanent
# firewall-cmd --reload
```
> 注：pg_hba.conf有5个参数，分别为：TYPE（主机类型）、DATABASE（数据库名）、USER（用户名）、ADDRESS（IP地址和掩码）、METHOD（加密方法）。“all”只有在没有其他的符合条目时才代表“所有”，优先级最低
## 卸载
```
1. systemctl stop postgresql-12
2. yum remove postgresql*
3. 删除目录
```