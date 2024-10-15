---
title: "CompletableFuture的用法"
date: 2024-10-12
---

## 异步调用，没有返回值

```java
CompletableFuture<Void> completableFuture1 = CompletableFuture.runAsync(()->{
    System.out.println(Thread.currentThread().getName()+"completableFuture1");
});
completableFuture1.get();
```
