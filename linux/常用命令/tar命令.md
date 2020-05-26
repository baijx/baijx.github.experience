# tar命令

> 参考 https://www.runoob.com/linux/linux-comm-tar.html

常见参数
```
操作模式
-c 创建一个新归档
-x 从归档中解出文件
-t 列出归档内容

压缩选项
-z 通过 gzip 过滤归档，对应后缀.tar.gz
-j 通过 bzip2 过滤归档，对应后缀.tar.bz2
-Z 通过 compress 过滤归档，对应后缀.tar.Z

提示性输出
-v 详细地列出处理的文件

设备选择和切换
-f 指定备份文件

解压指定目录
-C <目的目录>
```

## 1、压缩文件或文件夹
```
tar -cvf file.tar file.txt       // 压缩文件为.tar
tar -czvf file.tar.gz file.txt   // 压缩文件为.tar.gz
tar -czvf folder.tar.gz folder   // 压缩文件夹为.tar.gz
tar –cvf jpg.tar *.jpg		     //将目录里所有jpg文件打包成jpg.tar
```

## 2、解压文件
```
tar -xvf file.tar               // 解压文件.tar
tar -xvf file.tar -C folder     // 解压文件.tar到folder下
tar -xzvf test.tar.gz           // 解压文件.tar.gz
```

## 3、查看压缩文件内容
```
tar -tzvf folder.tar.gz
```
