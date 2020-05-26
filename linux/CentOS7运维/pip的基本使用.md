# pip的基本使用
> pip 是一个现代的，通用的 Python 包管理工具。提供了对 Python 包的查找、下载、安装、卸载的功能
## 安装pip
> Python 2.7.9 及后续版本, Python 3.4 及后续版本已经默认安装了pip。若是比较低版本的Python想要安装pip，可以执行下面命令安装
```
1. yum -y install epel-release
2. yum -y install python-pip
3. pip --version或者pip -V查看pip版本
```

## 安装模块
pip install <包名>

## 卸载模块
pip uninstall <包名>

## 查看已安装的包及其版本
pip freeze

## 查看可升级的包
pip list -o

## 升级指定包
pip install -U <包名>

## 升级pip
python -m pip install --upgrade pip

## 离线安装whl文件
> 有些库在安装的时候可能会遇到困难，这时我们可能需要离线安装whl文件来安装这个库，下面介绍一下具体操作步骤。 
```
1.下载所需包的whl文件
可以到通过下面的链接去找自己所需的包对应的whl文件
https://www.lfd.uci.edu/~gohlke/pythonlibs/
注意，每个whl文件名中的cp必须与你所用的python版本对应。如cp36**
指的是对应python3.6版本的文件

2.安装wheel模块
在命令行窗口使用命令pip install wheel 在线安装wheel模块

3.安装whl文件
在命令行窗口用cd命令跳转到whl文件所在目录，然后使用命令pip install ***.whl即可完成whl文件的安装。
```
