---
title: "Spring Cloud 引入Sentinel 实现熔断与限流"
date: 2024-10-22
---

## 添加pom依赖
```xml
<!--SpringCloud alibaba sentinel -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

## yml配置文件添加配置

```yml
spring:
  cloud:
    sentinel:
      transport:
        dashboard: localhost:8080 #配置Sentinel dashboard控制台服务地址
        port: 8719 #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
```
