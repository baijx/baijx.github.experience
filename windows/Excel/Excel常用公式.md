# Excel常用公式

# 提取单元格括号中的内容
```
=MID(A1,FIND("（", A1)+1,FIND("）", A1)-FIND("（", A1)-1)
```
![](./res/bracket.png)

# 提取指定字符之前/之后的字符串
```
=LEFT(A1,SEARCH("-",A1)-1)
=RIGHT(A1,LEN(A1)-SEARCH("-",A1))
```
![](./res/split.png)