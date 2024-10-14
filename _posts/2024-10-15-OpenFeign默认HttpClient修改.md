---
title: "OpenFeign默认HttpClient修改"
date: 2024-10-15
---

OpenFeign 中 http client 如果不做特殊配置，OpenFeign 默认使用 JDK 自带的 HttpURLConnection 发送 HTTP 请求，由于默认 HttpURLConnection 没有连接池、性能和效率比较低，如果采用默认，性能上不是最牛 B 的，所以加到最大。

## Apache HttpClient 5 替换 OpenFeign 默认的 HttpURLConnection

### 添加依赖

```xml
<!-- httpclient5-->
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
    <version>5.3</version>
</dependency>
<!-- feign-hc5-->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-hc5</artifactId>
    <version>13.1</version>
</dependency>
```

### Apache HttpClient5 配置开启

```yml
#  Apache HttpClient5 配置开启
spring:
  cloud:
    openfeign:
      httpclient:
        hc5:
          enabled: true
```

## 总结

使用 Apache HttpClient 5 替换 OpenFeign 默认的 HttpURLConnection 提升性能和效率。更换 Apache HttpClient 5 需要两步：添加添加依赖包，添加 yml 配置。
