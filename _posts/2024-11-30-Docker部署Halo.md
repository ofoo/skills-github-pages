---
title: "Docker部署Halo"
date: 2024-11-30
---





## 创建容器

```bash
docker run -it -d --name halo -p 8090:8090 -v ./halo2:/root/.halo2 -e JVM_OPTS="-Xmx256m -Xms256m" registry.fit2cloud.com/halo/halo:2.20
```

