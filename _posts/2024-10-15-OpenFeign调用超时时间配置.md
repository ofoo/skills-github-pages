---
title: "OpenFeign调用超时时间全局配置"
date: 2024-10-15
---

超时时间默认 60 秒。

## 全局配置

下面代码修改为 3 秒超时。

```yml
spring:
  cloud:
    openfeign:
      client:
        config:
          default:
            #连接超时时间
            connectTimeout: 3000
            #读取超时时间
            readTimeout: 3000
```

## 指定配置

```yml
spring:
  cloud:
    openfeign:
      client:
        config:
          #指定微服务名称，可指定配置
          cloud-service-system:
            #连接超时时间
            connectTimeout: 3000
            #读取超时时间
            readTimeout: 3000
```

## 总结

OpenFeign 超时时间可通过两种方式进行配置，第一种方式配置默认的，所有微服务都生效；第二种方式配置指定微服务的，指定微服务生效。默认和指定配置可共存，指定配置生效。
