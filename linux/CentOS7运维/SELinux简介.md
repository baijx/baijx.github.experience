# SELinux简介

## 一、前言

安全增强型 Linux（Security-Enhanced Linux）简称 SELinux，它是一个 Linux 内核模块，也是 Linux 的一个安全子系统。

## 二、SELinux 的作用及权限管理机制

SELinux 主要作用就是最大限度地减小系统中服务进程可访问的资源（最小权限原则）。

## 三、SELinux 的工作模式
SELinux 有三种工作模式，分别是：
1. enforcing：强制模式。违反 SELinux 规则的行为将被阻止并记录到日志中。
2. permissive：宽容模式。违反 SELinux 规则的行为只会记录到日志中。一般为调试用。
3. disabled：关闭 SELinux。

## 四、SELinux 的配置要点

### 查看SELinux状态：

1、/usr/sbin/sestatus -v      ##如果SELinux status参数为enabled即为开启状态

SELinux status:                 enabled

2、getenforce                 ##也可以用这个命令检查

### 关闭SELinux：

1、临时关闭（不用重启机器）：

setenforce 0                  ##设置SELinux 成为permissive模式

##setenforce 1 设置SELinux 成为enforcing模式

2、修改配置文件需要重启机器：

修改/etc/selinux/config 文件

将SELINUX=enforcing改为SELINUX=disabled

重启机器即可
