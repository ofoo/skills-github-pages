---
title: "OpenFeign打印日志"
date: 2024-10-15
---

## 是什么

Feign 提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节，说白了就是对Feign接口的调用情况进行监控和输出。

## 日志级别

- NONE：默认的，不显示任何日志；
- BASIC：仅记录请求方法、URL、响应状态码及执行时间；
- HEADERS：除了 BASIC 中定义的信息之外，还有请求和响应的头信息；
- FULL：除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据。

## 配置日志bean

```java
@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```

## 修改 yml 配置，开启日志的Feign客户端

公式(三段)：**logging.level** + 含有@FeignClient注解的完整带包名的接口名+debug

```yml
# feign日志以什么级别监控哪个接口
logging:
  level:
    com.github.ofoo.cloud.openfeign.system.SysUserClient: debug
```

## 总结

OpenFeign开启日志，需要修改java配置和yml配置。
