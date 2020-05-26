# zip 命令

## 0、安装 wget
```
yum install -y zip
yum install -y unzip
```

## 1、压缩单个文件
```
zip package.zip file
```

## 2、压缩多个文件
```
zip package.zip file1 file2
```

## 3、压缩单个文件夹
```
zip -r package.zip folder
```

## 4、压缩多个文件夹
```
zip -r package.zip folder1 folder2
```

## 5、压缩文件、文件夹
```
zip -r package.zip file1 folder1 file2
```

## 6、压缩时加密码
> 使用-e参数后会提示输入密码，解压缩时也会提示输入密码
```
zip -e package.zip file
```

# unzip 命令

## 1、在当前目录下解压缩
```
unzip package.zip
```

## 2、在指定目录下解压缩
```
unzip package.zip -d /tmp
```

## 3、不覆盖原有文件解压缩
```
unzip -n package.zip -d /tmp
```

## 4、覆盖原有文件解压缩
```
unzip -o package.zip -d /tmp
```

## 5、查看压缩文件目录，但不解压
```
unzip -v package.zip
```