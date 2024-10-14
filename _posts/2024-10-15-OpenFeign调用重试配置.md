---
title: "OpenFeign调用重试配置"
date: 2024-10-15
---

默认重试是关闭的。只调用一次就结束了。

## 添加配置类

```java
@Configuration
public class FeignConfig {

    @Bean
    public Retryer myRetryer() {
        return Retryer.NEVER_RETRY; //Feign默认配置是不走重试策略的

        //最大请求次数为3(1+2)，初始间隔时间为100ms，重试间最大间隔时间为1s
        // return new Retryer.Default(100,1,3);
    }

}
```

## 总结

OpenFeign 默认没有开启重试机制，需要添加配置类进行开启。
