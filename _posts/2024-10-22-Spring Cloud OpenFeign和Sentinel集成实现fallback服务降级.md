---
title: "Spring Cloud OpenFeign和Sentinel集成实现fallback服务降级"
date: 2024-10-23
---

## pom 添加依赖

```xml
<!--openfeign-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<!--SpringCloud alibaba sentinel -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

## yml 配置文件添加配置

```yml
spring:
  cloud:
    sentinel:
      transport:
        dashboard: localhost:8080 #配置Sentinel dashboard控制台服务地址
        port: 8719 #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
```

## 添加 openfeign 接口

**创建 PayFeignSentinelApi 接口**

```java
@FeignClient(value = "cloud-service-system",fallback = PayFeignSentinelApiFallBack.class)
public interface PayFeignSentinelApi
{
    @GetMapping("/pay/nacos/get/{orderNo}")
    public R getPayByOrderNo(@PathVariable("orderNo") String orderNo);
}
```

**创建 PayFeignSentinelApiFallBack 类，实现 PayFeignSentinelApi 接口**

```java
@Component
public class PayFeignSentinelApiFallBack implements PayFeignSentinelApi
{
    @Override
    public R getPayByOrderNo(String orderNo)
    {
        return R.error("对方服务宕机或不可用，FallBack服务降级o(╥﹏╥)o");
    }
}
```

## 消费者服务，yml 配置文件添加配置

```yml
# 激活Sentinel对Feign的支持
feign:
  sentinel:
    enabled: true
```
