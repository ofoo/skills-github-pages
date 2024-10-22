---
title: "Spring Cloud 使用nacos作为注册中心"
date: 2024-10-21
---

## pom 引入依赖

```xml
<!--nacos-discovery-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```
如果会调用其他服务，需要添加下面依赖
```xml
<!--loadbalancer-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

## 添加 yml 配置

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-service-system
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址
```
## 启动类添加注解

```java
@EnableDiscoveryClient
```