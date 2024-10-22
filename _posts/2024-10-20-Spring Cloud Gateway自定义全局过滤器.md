---
title: "Spring Cloud Gateway自定义全局过滤器"
date: 2024-10-21
---

## 介绍
全局过滤器对所有路由生效。

## 新建类MyGlobalFilter并实现GlobalFilter,Ordered两个接口

自定义接口调用耗时统计的全局过滤器

```java
@Component
@Slf4j
public class MyGlobalFilter implements GlobalFilter, Ordered {

    public static final String BEGIN_VISIT_TIME="begin_visit_time";//开始调用方法的时间

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        //1 先记录下访问接口的开始时间
        exchange.getAttributes().put(BEGIN_VISIT_TIME,System.currentTimeMillis());
        return chain.filter(exchange).then(Mono.fromRunnable(()->{
            Long beginVisitTime = exchange.getAttribute(BEGIN_VISIT_TIME);
            if (Objects.nonNull(beginVisitTime)){
                log.info("访问接口主机："+exchange.getRequest().getURI().getHost());
                log.info("访问接口端口："+exchange.getRequest().getURI().getPort());
                log.info("访问接口URL："+exchange.getRequest().getURI().getPath());
                log.info("访问接口URL后面参数："+exchange.getRequest().getURI().getRawQuery());
                log.info("访问接口时长："+(System.currentTimeMillis()-beginVisitTime)+"毫秒");
                log.info("================分割线================");
                System.out.println();
            }
        }));
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
```
