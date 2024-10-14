---
title: "OpenFeign请求和响应压缩配置"
date: 2024-10-15
---

**对请求和响应进行 GZIP 压缩**

Spring Cloud OpenFeign 支持对请求和响应进行 GZIP 压缩，以减少通信过程中的性能损耗。
通过下面的两个参数设置，就能开启请求与相应的压缩功能：

```
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.response.enabled=true
```

**细粒度化设置**

对请求压缩做一些更细致的设置，比如下面的配置内容指定压缩的请求数据类型并设置了请求压缩的大小下限，
只有超过这个大小的请求才会进行压缩：

```
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.request.mime-types=text/xml,application/xml,application/json #触发压缩数据类型
spring.cloud.openfeign.compression.request.min-request-size=2048 #最小触发压缩的大小
```

## 修改 yml 配置

```yml
compression:
  request:
    enabled: true
    min-request-size: 2048 #最小触发压缩的大小
    mime-types: text/xml,application/xml,application/json #触发压缩数据类型
  response:
    enabled: true
```

## 总结

启用请求、响应压缩配置，只需修改 yml 配置文件添加以上配置就可以。
