# Vmware虚机上网

## Vmware配置
```
编辑→虚拟网络编辑器→更改设置→NAT模式：
子网IP：192.168.105.0
子网掩码：255.255.255.0
NAT设置→网关IP:192.168.105.2
DHCP设置：起始IP地址：192.168.105.100
结束IP地址：192.168.105.254
```

## CentOS7网络设置
```
# cd /etc/sysconfig/network-scripts/
# ls --第一个ifcfg-ens33就是要编辑的CentOS7网络配置文件，具体文件名可能有差异，前缀都是ifcfg-ens
# vim ifcfg-ens33
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
#BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=8e4e1e17-8826-4d24-a8ac-ffc2da3a60db
DEVICE=ens33
ONBOOT=yes
IPADDR=192.168.105.120
NETMASK=255.255.255.0
GATEWAY=192.168.105.2
#DNS1=114.114.114.114 --DNS与能上网的主机保持一致即可
#DNS2=8.8.8.8
PREFIX=24
ZONE=public
```

## 重启网络
```
systemctl restart network
```

## 验证
```
# ping www.baidu.com
```

## 其他
> 如果还是不能上网，检查以下内容
* 是否存在ifcfg-ens33.bak之类的文件，删除
* 查看本机网关，route -n
* 如果网关不对，vim /etc/resolv.conf，nameserver 192.168.105.2



