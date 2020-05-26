# 修改yum源为国内yum源

## 一、更新步骤

1. 备份本地yum源
```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo_bak
```

2. 获取阿里yum源配置文件
```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

3. 更新cache
```
yum makecache
```


4. 更新系统（没必要）
> 生产环境对软件版本和内核版本要求非常精确，别没事有事随便的进行yum update操作
```
yum -y update
```

5. 查看当前yum源
```
# yum repolist
```

## 二、yum -y update的意义

通常你拿到的服务器都是软件包版本比较低的操作系统，yum install的时候对于依赖的软件包不会自动升级，会报错依赖冲突，此时你需要用yum update/upgrade更新下系统的软件包，以满足安装依赖。

首先yum update和yum upgrade的功能是一样的，都是将需要更新的package更新至软件源中的最新版。

唯一不同是：yum upgrade会删除旧版本的package，而yum update则会保留。注意！如果你的某些软件依赖旧版本的package，请使用yum update。