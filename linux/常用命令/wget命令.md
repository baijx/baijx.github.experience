# wget命令

> 参考 https://www.cnblogs.com/zhoul/p/9939601.html

## 0、安装 wget
```
yum install -y wget
```

## 1、使用 wget 下载单个文件
> 从网络下载一个文件并保存在当前目录
```
wget http://f.hiphotos.baidu.com/zhidao/pic/item/b58f8c5494eef01ff772eac9e1fe9925bc317d5c.jpg
```

## 2、使用 wget -O 下载并以不同的文件名保存
> 使用参数-O来指定一个文件名
```
wget -O bbb.jpg http://f.hiphotos.baidu.com/zhidao/pic/item/b58f8c5494eef01ff772eac9e1fe9925bc317d5c.jpg
```

## 3、使用 wget -c 断点续传 
> 下载大文件时突然由于网络等原因中断，可以使用wget -c重新启动下载中断的文件而不是重新下载
```
wget -c http://f.hiphotos.baidu.com/zhidao/pic/item/b58f8c5494eef01ff772eac9e1fe9925bc317d5c.jpg
```

## 4、使用 wget -P 下载到指定目录
> 使用参数-P来指定一个文件目录
```
wget -P /home/softwares http://f.hiphotos.baidu.com/zhidao/pic/item/b58f8c5494eef01ff772eac9e1fe9925bc317d5c.jpg
```