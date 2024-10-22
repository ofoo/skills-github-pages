---
title: "Spring Cloud Gateway自定义条件过滤器"
date: 2024-10-21
---

## 介绍
条件过滤器对单个路由生效。

## 新建类名XXX需要以GatewayFilterFactory结尾，并继承AbstractGatewayFilterFactory类

```java
@Component //标注不可忘
public class MyGatewayFilterFactory extends AbstractGatewayFilterFactory<MyGatewayFilterFactory.Config>{

}
```
## 新建XXXGatewayFilterFactory.Config内部类
```java
@Component
public class MyGatewayFilterFactory extends AbstractGatewayFilterFactory<MyGatewayFilterFactory.Config> {
    public static class Config {
        @Setter @Getter
        private String status;
    }
}
```
## 重写apply方法
```java
@Component
public class MyGatewayFilterFactory extends AbstractGatewayFilterFactory<MyGatewayFilterFactory.Config> {
    @Override
    public GatewayFilter apply(MyGatewayFilterFactory.Config config) {
        return new GatewayFilter() {
            @Override
            public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
                ServerHttpRequest request =  exchange.getRequest();
                System.out.println("进入自定义网关过滤器MyGatewayFilterFactory，status===="+config.getStatus());
                if(request.getQueryParams().containsKey("atguigu")) {
                    return chain.filter(exchange);
                }else {
                    exchange.getResponse().setStatusCode(HttpStatus.BAD_REQUEST);
                    return exchange.getResponse().setComplete();
                }
            }
        };
    }

    public static class Config {
        @Setter @Getter
        private String status;
    }
}
```

## 重写shortcutFieldOrder
```java
@Override
public List<String> shortcutFieldOrder() {
    List<String> list = new ArrayList<String>();
    list.add("status");
    return list;
}
```

## 空参构造方法，内部调用super
```java
public MyGatewayFilterFactory() {
    super(MyGatewayFilterFactory.Config.class);
}
```

## 完整代码
```java
@Component
public class MyGatewayFilterFactory extends AbstractGatewayFilterFactory<MyGatewayFilterFactory.Config> {
    public MyGatewayFilterFactory() {
        super(MyGatewayFilterFactory.Config.class);
    }


    @Override
    public GatewayFilter apply(MyGatewayFilterFactory.Config config) {
        return new GatewayFilter()
        {
            @Override
            public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain)
            {
                ServerHttpRequest request = exchange.getRequest();
                System.out.println("进入了自定义网关过滤器MyGatewayFilterFactory，status："+config.getStatus());
                if(request.getQueryParams().containsKey("atguigu")){
                    return chain.filter(exchange);
                }else{
                    exchange.getResponse().setStatusCode(HttpStatus.BAD_REQUEST);
                    return exchange.getResponse().setComplete();
                }
            }
        };
    }

    @Override
    public List<String> shortcutFieldOrder() {
        return Arrays.asList("status");
    }

    public static class Config {
        @Getter@Setter
        private String status;//设定一个状态值/标志位，它等于多少，匹配和才可以访问
    }
}
```
## yml添加配置
`- My=atguigu`

```yml
spring:
  application:
    name: cloud-gateway #以微服务注册进consul或nacos服务列表内
  cloud:
    gateway:
      routes:
        - id: pay_routh3 #pay_routh3
          uri: lb://cloud-payment-service                #匹配后提供服务的路由地址
          predicates:
            - Path=/pay/gateway/filter/**              # 断言，路径相匹配的进行路由，默认正确地址
          filters:
            - My=atguigu
```
