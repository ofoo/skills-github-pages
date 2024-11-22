---
title: "Spring Cloud 引入Seata分布式事务"
date: 2024-10-30
---

## 介绍

引入事务可以保证一系列操作，要么全成功，要么全不成功。分布式事务适用于微服务架构，可以实现服务和服务之间的一些列操作，要么全成功，要么全不成功。

## 添加 pom 依赖

```xml
<!--alibaba-seata-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
</dependency>
```

## yml 配置文件添加配置

```yml
# ========================seata===================
seata:
  registry:
    type: nacos
    nacos:
      server-addr: 127.0.0.1:8848
      namespace: ${custom.nacos.namespace}
      application: seata-server
  tx-service-group: default_tx_group # 事务组，由它获得TC服务的集群名称
  service:
    vgroup-mapping: # 点击源码分析
      default_tx_group: default # 事务组与TC服务集群的映射关系
  data-source-proxy-mode: AT

logging:
  level:
    io:
      seata: info
```

## 业务类方法添加注解

添加注解后可以实现不同服务，执行的操作，要么全成功，要么全不成功

```java
@GlobalTransactional(name = "zzyy-create-order",rollbackFor = Exception.class) //AT
public void create(TOrder order) {
  //执行添加方法
  //调用服务1
  //调用服务2
}
```
