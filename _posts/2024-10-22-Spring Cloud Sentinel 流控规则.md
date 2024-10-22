---
title: "Spring Cloud Sentinel 流控规则"
date: 2024-10-22
---

## 流控模式

### 直接
一秒请求两个，超过则失败。

![alt text](image.png)

### 关联
`/testA`请求，一秒请求1个，超过1个，限流`/testB`请求

![alt text](image-1.png)

### 链路
`common()`方法同时被`/testC`和`testD`调用，实施针对性的不同限流措施，比如`/testC`请求来访问就限流，`testD`请求来访问就不限流

![alt text](image-2.png)

## 流控效果
### 直接
快速失败。

### 预热WarmUp
5秒钟内每秒可访问3次，过了保护期5秒后每秒可访问10次

![alt text](image-3.png)

### 排队等待
一秒钟通过一个请求，10秒后的请求作为超时处理，放弃

![alt text](image-4.png)
