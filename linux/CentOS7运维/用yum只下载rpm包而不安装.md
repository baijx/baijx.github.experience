# 用yum只下载rpm包而不安装

## yum 下载软件的存放位置
一般情况下，yum install安装软件后rpm包存放在/var/cache/yum/*/packages下。如
/var/cache/yum/i386/6/updates/packages/openssl-1.0.0-4.el6_0.2.i686.rpm

通常安装后删除，但亦可通过配置保留。

配置yum.conf
keepcache选项 keepcache=1

## 只下载rpm包

### 安装yum-utils
```
# yum -y install yum-utils
```

### 用yumdownloader下载rpm包到当前目录
```
# yumdownloader ncftp
```

### rpm强制安装
> 有时用yum自动安装会不成功时，就可以下载该rpm包后，再强制安装。如
```
rpm -ivh openssl-1.0.0-4.el6_0.2.i686.rpm --force --nodeps
```
