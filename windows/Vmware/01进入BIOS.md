# 进入BIOS

找到虚机的vmx文件，文本编辑，添加bios.bootDelay参数，这样开机时，就会倒计时，可以选择进入BIOS
```
.encoding = "GBK"
bios.bootDelay = "5000"
config.version = "8"
virtualHW.version = "8"
```