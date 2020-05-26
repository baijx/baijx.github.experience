# 将用户添加到sudoers里

1. 先修改sudoers的配置文件权限
```
chmod u+w /etc/sudoers
```

2. 找到root ALL=(ALL) ALL这一行，在下面追加一行
```
yourusername ALL=(ALL) ALL
```

3. 撤销sudoers的配置文件的写权限
```
chmod u-w /etc/sudoers
```