---
title: "Docker运行Nginx"
date: 2024-12-30
---

## 介绍

Docker运行Nginx，Nginx版本采用1.26。

命令包含挂在宿主机内容目录和nginx配置文件。

## 命令

```bash
docker run --name some-nginx -p 9090:80 -v /Users/mac/docker_nginx/content:/usr/share/nginx/html:ro -v /Users/mac/docker_nginx/nginx.conf:/etc/nginx/nginx.conf:ro -d nginx
```

## 获取nginx配置文件

```bash
docker run --rm --entrypoint=cat nginx:1.26 /etc/nginx/nginx.conf > nginx.conf
```
