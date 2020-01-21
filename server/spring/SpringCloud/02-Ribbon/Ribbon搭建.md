## Ribbon搭建

## Eureka搭建
> 参考Eureka Server单节点

## Server Provider搭建

### pom.xml
> 参考Eureka Client

### application.yml
```yml
spring:
  application:
    name: server-provider
  profiles:
    active: provider1
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    serviceUrl:
      defaultZone: http://localhost:18080/eureka/
#      defaultZone: http://server1:18081/eureka/,http://server2:18082/eureka/,http://server3:18083/eureka/
```

### application-provider1.yml
```yml
server:
  port: 28081
```

### application-provider2.yml
```yml
server:
  port: 28082
```

### application-provider3.yml
```yml
server:
  port: 28083
```
### ServerProviderApplication
```java
@SpringBootApplication
@EnableEurekaClient
public class ServerProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServerProviderApplication.class, args);
    }
}
```

### HelloController
```java
@RestController
public class HelloController {
    @Value("${server.port}")
    String port;

    @RequestMapping("/hello")
    public String hello(@RequestParam("name") String name) {
        return "Hello " + name + ", I am from port " + port;
    }
}
```

## Ribion搭建

### pom.xml
> 注意添加spring-cloud-starter-netflix-ribbon
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.deepin</groupId>
    <artifactId>ribbon</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.7.RELEASE</version> <!-- lookup parent from repository -->
        <relativePath/>
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### application.yml
```yml
spring:
  application:
    name: ribbon-server
server:
  port: 38080
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:18080/eureka/
```

### RibbonServerApplication
```java
@EnableEurekaClient
@SpringBootApplication
public class RibbonServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(RibbonServerApplication.class, args);
    }

    @Bean // 将这个对象放入spring容器中
    @LoadBalanced // 如果要使用注册中心的服务别名访问, 或者想使用本地负载均衡，就必须要加这个注解
    RestTemplate restTemplate() {
        return new RestTemplate();
    }

}
```

### HelloService
```java
@Service
public class HelloService {

    @Autowired
    RestTemplate restTemplate;

    public String hello(String name) {
        // URL使用服务别名
        return restTemplate.getForObject("http://server-provider/" + "hello?name=" + name, String.class);
    }
}
```

### HelloController
```java
@RestController
public class HelloController {

    @Autowired
    HelloService helloService;

    @RequestMapping("/hello")
    public String hello(@RequestParam("name") String name) {
        return helloService.hello(name);
    }
}
```

## 测试结果
- 启动eureka
- 启动三个service provider
- 启动ribbon

> erueka注册情况：

![](./res/eureka.png)

> 通过Ribbon调用服务接口，刷新获得不同的结果：http://localhost:38080/hello?name=zhangsan

![](./res/ribbon1.png)

![](./res/ribbon2.png)

![](./res/ribbon3.png)