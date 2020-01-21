# Feign简介


Feign是声明式的web service客户端，它让微服务之间的调用变得更简单了，类似controller调用service。

默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果。

整合了Ribbon和Hystrix，从而不再需要显式地使用这两个组件。但是要用Ribbon和Hystrix自带的注解必须要引入相应的jar包才可以

