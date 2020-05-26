# du命令

> du命令是检查硬盘使用情况，统计文件或目录及子目录使用硬盘的空间大小。

## 命令格式
```
du [选项][文件]

-a
显示所有目录或文件的大小
-b
以byte为单位，显示目录或文件的大小
-c
显示目录或文件的总和
-k
以KB为单位输出
-m
以MB为单位输出
-s
仅显示目录或文件的总计数值
-h
以K,M,G为单位，提高信息可读性
-x
跳过不同的文件系统目录
-S
显示目录的大小，但不含子目录大小。
-D
显示指定符号链接的源文件大小
```

## du ：显示当前目录及子目录的大小（kb）
```
[root@deepin softwares]# du
0       ./folder
629836  .                //最下面显示的是当前目录总大小
```

## du [文件] ： 显示指定文件或目录所占空间
```
# du mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz 
629804  mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
```

## du -ah [目录] ：列出目录及其子目录下所有目录和文件的大小
```
# du -ah folder
0       folder/file.txt
0       folder/aaa.txt
0       folder
```

## du [多个文件或目录]：查看多个指定文件或目录的大小
```
# du aaa.tar aaa.txt folder
12      aaa.tar
0       aaa.txt
0       folder
```

## du -sh [文件]：以K,M,G为单位，提高信息可读性
```
# du -sh mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz 
616M    mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
```

## du -s 目录/*|sort -nr 指定目录从大到小排列
```
# du -s ./*|sort -nr
629804  ./mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
12      ./aaa.tar
4       ./folder.tar.gz
0       ./folder
0       ./aaa.txt
```