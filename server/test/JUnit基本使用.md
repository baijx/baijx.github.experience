# JUnit基本使用

## pom.xml引入JUnit
```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

## 样例
```java
import org.junit.*;

public class JunitDemo {

    @BeforeClass
    public static void testBeforeClass() {
        System.out.println("beforeClass");
    }

    @Before
    public void testBefore() {
        System.out.println("before");
    }

    @Test
    public void testHello() {
        System.out.println("hello");
    }

    @Test(expected = Exception.class)
    public void testException() throws Exception {
        throw new Exception();
    }

    @Test(timeout = 3000)
    public void testTimeout() {
        for (; ; ) {

        }
    }

    @Ignore
    @Test
    public void testIgnore() {
        System.out.println("ignore");
    }

    @After
    public void testAfter() {
        System.out.println("after");
    }

    @AfterClass
    public static void testAfterClass() {
        System.out.println("afterClass");
    }
}
```

## 注解说明
> 一个JUnit4的单元测试用例执行顺序为: @BeforeClass -> @Before -> @Test -> @After -> @AfterClass; 
### 1. 注解@BeforeClass
在所有的方法运行前被执行，只执行一次，static void修饰

### 2. @Before
在每一个测试方法被运行前执行一次，常用来进行一些测试环境的准备

### 3. @Test
1） @Test方法必须是public的

2） @Test方法必须是void的，不返回

3） @Test方法必须是空参

4） @Test方法必须是非静态

5） @Test(timeout=3000)如果方法的执行操作所耗费的毫秒数>3000毫秒，则测试方法失败

6） @Test(expected=Exception.class) 检查测试方法是不是抛出了对应的异常

### 4. @After
在每个测试方法被运行后执行一次（常与与@Before进行对应，做一个清理工作）

### 5. @AfterClass
在所有的方法运行后被执行，只执行一次，static void修饰

### 6. @Ignore
忽略该测试方法