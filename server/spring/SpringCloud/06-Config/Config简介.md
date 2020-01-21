# Config简介

## 使用场景

在普通单体应用，我们常使用配置文件(application(*).properties(yml))管理应用的所有配置。这些配置文件在单体应用中非常胜任其角色，并没有让我们感觉到有头疼的地方。但随着微服务框架的引入，微服务数量就会在我们产品中不断增加，之前我们重点考虑的是系统的可伸缩、可扩展性好，但随之就是配置管理的问题就会一一暴露出来。起初微服务器各自管各自的配置，在开发阶段并没什么问题，但到了生产环境管理就会很头疼，如果要大规模更新某项配置，困难就可想而知。

为了方便服务配置文件统一管理，更易于部署、维护，所以就需要分布式配置中心组件了，在spring cloud中，有分布式配置中心组件spring cloud config。**配置服务中心默认采用Git的方式进行存储，它也支持配置文件放在在配置服务的内存中**。引入spring cloud config后，我们的外部配置文件就可以集中放置在一个git仓库里，再新建一个config server，用来管理所有的配置文件，维护的时候需要更改配置时，只需要在本地更改后，推送到远程仓库，所有的服务实例都可以通过config server来获取配置文件，这时每个服务实例就相当于配置服务的客户端config client,**为了保证系统的稳定，配置服务端config server可以进行集群部署，即使某一个实例，因为某种原因不能提供服务，也还有其他的实例保证服务的继续进行**

## 常见配置

1. 配置文件 统一集中配置

2. 每个模块的数据库配置、缓存配置 独立出来

3. 通用配置独立出来

## Spring项目配置加载顺序
- 这里是列表文本命令行参数
- SPRING_APPLICATION_JSON 参数
- 从java:comp/env 加载 JNDI 属性
- Java系统属性 （System.getProperties()）
- 操作系统环境变量
- 如果有使用 random.* 属性配置，则使用 RandomValuePropertySource 产生
- 外部特定应用配置文件 例如：application-{profile}.properties 或者 YAML variants
- 内部特定应用配置文件 例如：application-{profile}.properties 或者 YAML variants
- 外部应用配置文件 例如：application.properties 或者 YAML variants
- 内部应用配置文件 例如：application.properties 或者 YAML variants
- 加载@Configuration类的 @PropertySource 或者 @ConfigurationProperties 指向的配置文件
- 默认配置，通过SpringApplication.setDefaultProperties 设置

## 配置规则详解
Config Client从Config Server中获取配置数据的流程:

1. Config Client启动时，根据bootstrap.properties中配置的应用名称(application)、环境名(profile)和分支名(label)，向Config Server请求获取配置数据;
2. Config Server根据Config Client的请求及配置，从Git仓库(这里以Git为例)中查找符合的配置文件;
3. Config Server将匹配到的Git仓库拉取到本地，并建立本地缓存;
4. Config Server创建Spring的ApplicationContext实例，并根据拉取的配置文件，填充配置信息，然后将该配置信息返回给Config Client;
5. Config Client获取到Config Server返回的配置数据后，将这些配置数据加载到自己的上下文中。同时，因为这些配置数据的优先级高于本地Jar包中的配置，因此将不再加载本地的配置。

那么，Config Server又是如何与Git仓库中的配置文件进行匹配的呢？通常，我们会为一个项目建立类似如下的配置文件:
- mallweb.properties: 基础配置文件;
- mallweb-dev.properties: 开发使用的配置文件;
- mallweb-test.properties: 测试使用的配置文件;
- mallweb-prod.properties: 生产环境使用的配置文件;

当我们访问Config Server的端点时，就会按照如下映射关系来匹配相应的配置文件：

- /{application}/{profile}[/{label}]
- /{application}-{profile}.yml
- /{label}/{application}-{profile}.yml
- /{application}-{profile}.properties
- /{label}/{application}-{profile}.properties
上面的Url将会映射为格式为:{application}-{profile}.properties(yml)的配置文件。另外，label则对应Git上分支名称，是一个可选参数，如果没有则为默认的master分支。

而Config-Client的bootstrap.properties配置对应如下:

- spring.application.name <==> application;
- spring.cloud.config.profile <==> profile;
- spring.cloud.config.label <==> label.

