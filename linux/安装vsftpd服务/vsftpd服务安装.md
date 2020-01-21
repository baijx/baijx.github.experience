# vsftpd服务安装

## 卸载旧服务

### 查找vsftpd服务
```
# rpm -aq vsftpd
vsftpd-3.0.2-25.el7.x86_64
```

### 删除vsftpd服务
```
# systemctl stop  vsftpd.service
rpm -e vsftpd-3.0.2-25.el7.x86_64
返回结果显示：
warning: /etc/vsftpd/vsftpd.conf saved as /etc/vsftpd/vsftpd.conf.rpmsave #删除时将备份vsftp的用户列表文件
```

## 安装vsftpd组件

### yum安装
```
# yum -y install vsftpd
```

### 启动ftp
```
# systemctl start vsftpd.service
```

### 开放21端口
```
# firewall-cmd --zone=public --add-port=21/tcp --permanent
# firewall-cmd --reload
```

## vsftpd服务的详细配置
> 以上已经可以匿名用户访问ftp了，但实践中为了安全会增强设置

### 建立ftp账户
> 新建一个不能登录系统用户，只用来登录ftp服务的用户。这里如果没设置用户目录，默认是在home下
```
# useradd ftpuser -s /sbin/nologin
# passwd ftpuser
```

### 设置ftp的状态
```
# getsebool -a|grep ftp

ftpd_anon_write --> off
ftpd_connect_all_unreserved --> off
ftpd_connect_db --> off
ftpd_full_access --> off
ftpd_use_cifs --> off
ftpd_use_fusefs --> off
ftpd_use_nfs --> off
ftpd_use_passive_mode --> off
httpd_can_connect_ftp --> off
httpd_enable_ftp_server --> off
tftp_anon_write --> off
tftp_home_dir --> off
```
将ftpd_full_access和tftp_home_dir两项设置为on
```
# setsebool -P allow_ftpd_full_access on
# setsebool -P tftp_home_dir on
```

### 设置vsftpd配置
```
# vim /etc/vsftpd/vsftpd.conf
```

关闭匿名用户
```
anonymous_enable=NO
#anon_upload_enable=YES
#anon_mkdir_write_enable=YES
```

开启黑名单
>userlist_deny=YES，则user_list是黑名单；userlist_deny=NO，则user_list是白名单
```
userlist_enable=YES
userlist_deny=YES
userlist_file=/etc/vsftpd/user_list
```

user_list样例
```
# vsftpd userlist
# If userlist_deny=NO, only allow users in this file
# If userlist_deny=YES (default), never allow users in this file, and
# do not even prompt for a password.
# Note that the default vsftpd pam config also checks /etc/vsftpd/ftpusers
# for users that are denied.
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
news
uucp
operator
games
nobody
```

锁定ftp用户目录，禁止访问用户目录以外的目录
> 注意添加allow_writeable_chroot=YES。从2.3.5之后，vsftpd增强了安全检查，如果用户被限定在了其主目录下，则该用户的主目录不能再具有写权限了！如果检查发现还有写权限，就会报该错误：500 OOPS: vsftpd: refusing to run with writable root inside chroot ()。还可以chmod a-w /home/ftpuser去除用户主目录的写权限解决此错误。
```
chroot_local_user=NO
allow_writeable_chroot=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd.chroot_list
```

vim /etc/vsftpd.chroot_list添加ftpuser

开启PSAV
```
pasv_enable=YES
pasv_min_port=30000
pasv_max_port=30999
```

最终vsftpd.conf
```
tcp_wrappers=YES
# (default follows)
#chroot_list_file=/etc/vsftpd/chroot_list
#
# You may activate the "-R" option to the builtin ls. This is disabled by
  default to avoid remote users being able to cause excessive I/O on large
▽ sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
#ls_recurse_enable=YES
#
# When "listen" directive is enabled, vsftpd runs in standalone mode and
# listens on IPv4 sockets. This directive cannot be used in conjunction
# with the listen_ipv6 directive.
listen=NO
#
# This directive enables listening on IPv6 sockets. By default, listening
# on the IPv6 "any" address (::) will accept connections from both IPv6
# and IPv4 clients. It is not necessary to listen on *both* IPv4 and IPv6
# sockets. If you want that (perhaps because you want to listen on specific
# addresses) then you must run two copies of vsftpd with two configuration
# files.
# Make sure, that one of the listen options is commented !!
listen_ipv6=YES

pam_service_name=vsftpd

userlist_enable=YES
userlist_deny=YES
userlist_file=/etc/vsftpd/user_list

chroot_local_user=NO
allow_writeable_chroot=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd.chroot_list

pasv_enable=YES
pasv_min_port=30000
pasv_max_port=30999

```

### 开启端口
```
# firewall-cmd --zone=public --add-port=30000-30999/tcp 
# firewall-cmd --reload
```

## 530 Login incorrect解决方法
> 客户端登录ftp如果报530 Login incorrect，可以按如下方法解决
```
# vim /etc/pam.d/vsftpd

#%PAM-1.0
session    optional     pam_keyinit.so    force revoke
auth       required     pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
auth       required    pam_shells.so
auth       include      password-auth
account    include      password-auth
session    required     pam_loginuid.so
session    include      password-auth
```

1. 查看上面的vim /etc/vsftpd/ftpusers，确保账号没有在这个文件内。
2. 将auth required pam_shells.so修改为->auth required pam_nologin.so 即可或者直接注释掉这行，重启ftp服务

### 设置开机启动
```
# systemctl enable vsftpd
```

## 启停命令
```
systemctl start vsftpd.service
systemctl restart vsftpd.service
systemctl status vsftpd.service
systemctl stop  vsftpd.service
```
