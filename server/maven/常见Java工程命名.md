# 常见Java工程命名

## groupId命名

| 命名规则            | 示例                                      |
|-----------------|-----------------------------------------|
| com\.公司         | com\.ibm com\.alibaba                   |
| com\.公司\.部门/功能  | com\.netflix\.eureka com\.google\.guava |

## artifactId命名
> 一般控制在四级以内，用-或者.分隔

| 命名规则 | 示例                                                          |
|------|-------------------------------------------------------------|
| 一级   | guava fastjson dubbo                                        |
| 二级   | guava\-parent eureka\-core                                  |
| 三级   | google\-http\-client com\.ibm\.mq                           |
| 四级   | google\-http\-client\-jackson2 com\.ibm\.mq\.commonservices |

## package命名

| 命名规则                  | 示例                                                               |
|-----------------------|------------------------------------------------------------------|
| com\.公司\.项目           | com\.alibaba\.dubbo com\.alibaba\.fastjson com\.google\.protobuf |
| com\.公司\.部门/项目\.功能模块  | com\.google\.api\.client                                         |

