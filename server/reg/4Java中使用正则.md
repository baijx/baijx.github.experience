# Java中使用正则

> 在Java中使用正则表达式，可以达到匹配、查找、替换字符串的目的。在Java字符串中，原本正则表达式的一个\要用两个。

## 正则相关的类
java.util.regex 包主要提供了以下两个类：
 - Pattern类，是一个正则表达式的编译表示
 - Matcher类，对输入字符串进行匹配和替换等操作

在实际开发中，为了方便，我们一般使用String类的方法达到目的：
 - 验证：boolean matches(String regex)
 - 拆分: String[] split(String regex)
 - 替换： String replaceAll(String regex, String replacement)

## 验证判定
```java
public class RegxDemo1 {
    public static void main(String[] args) {
        String str = "abc123";
        String reg = "\\w{6}";
        System.out.println(str.matches(reg)); // true

        Pattern pat = Pattern.compile(reg);
        Matcher mat = pat.matcher(str);
        System.out.println(mat.matches()); // true
    }
}
```

## 查找替换
```java
public class RegxDemo2 {
    public static void main(String[] args) {
        String str = "It is a dog";
        String reg = "dog";
        String rep = "cat";
        System.out.println(str.replaceAll(reg, rep)); // It is a cat

        Pattern pat = Pattern.compile(reg);
        Matcher mat = pat.matcher(str);
        System.out.println(mat.replaceAll(rep)); // It is a cat
    }
}

```

## 捕获分组
> 捕获分组是按正则表达是的分组进行捕获。捕获的分组group(0)代表整个表达式，之后捕获按左括号出现的位置依次排序。可以通过调用 matcher 对象的 groupCount 方法来查看表达式有多少个分组。例如，在表达式((A)(B(C)))就会依次捕获以下4个分组：
 - ((A)(B(C)))
 - (A)
 - (B(C))
 - (C)
```java
public class RegxDemo3 {
    public static void main(String[] args) {
        String str = "My name is Zhangsan,18 years old";
        String reg = "My name is (.+),(\\d+) years old";

        Pattern pat = Pattern.compile(reg);
        Matcher mat = pat.matcher(str);
        System.out.println(mat.groupCount());
        if (mat.find()) {
            for (int i = 0; i <= mat.groupCount(); i++) {
                System.out.println("Found value:" + mat.group(i));
            }
        }
    }
}
```