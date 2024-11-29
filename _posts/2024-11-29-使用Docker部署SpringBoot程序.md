---
title: "使用Docker部署SpringBoot程序"
date: 2024-11-29
---

在使用Docker部署Spring Boot 应用程序时，可以将 Spring Boot 应用打包成一个独立的 JAR 文件，并构建 Docker 镜像以进行部署。以下是完整的步骤：

---

## **1. 准备 Spring Boot 程序**
### **1.1 打包 Spring Boot 项目**
1. 在 Spring Boot 项目目录下执行 Maven 或 Gradle 构建命令：
    - Maven:
      ```bash
      mvn clean package
      ```
    - Gradle:
      ```bash
      gradle clean build
      ```
2. 生成的 JAR 文件通常位于 `target/` 或 `build/libs/` 目录下。例如：
    - `target/myapp-0.0.1-SNAPSHOT.jar`

---

## **2. 创建 Dockerfile**
在项目的根目录下创建一个名为 `Dockerfile` 的文件，内容如下：

```dockerfile
# 使用官方基础镜像（选择 OpenJDK）
FROM openjdk:17-jdk-slim

# 设置应用程序的工作目录
WORKDIR /app

# 将 JAR 文件复制到容器中
COPY target/myapp-0.0.1-SNAPSHOT.jar app.jar

# 运行 Spring Boot 应用
ENTRYPOINT ["java", "-jar", "app.jar"]

# 可选：指定容器对外暴露的端口（Spring Boot 默认端口为 8080）
EXPOSE 8080
```

> **注意**: 将 `myapp-0.0.1-SNAPSHOT.jar` 替换为实际生成的 JAR 文件名。

---

## **3. 构建 Docker 镜像**
1. 打开终端，进入项目目录。
2. 执行以下命令构建 Docker 镜像：
   ```bash
   docker build -t my-springboot-app .
   ```

- `-t my-springboot-app` 是镜像的名称。
- `.` 指定 Dockerfile 的位置（当前目录）。

3. 构建完成后，通过以下命令查看镜像：
   ```bash
   docker images
   ```

---

## **4. 运行 Docker 容器**
1. 启动容器：
   ```bash
   docker run -d -p 8080:8080 --name my-springboot-container my-springboot-app
   ```
    - `-d`：后台运行容器。
    - `-p 8080:8080`：将本地主机的 8080 端口映射到容器的 8080 端口。
    - `--name`：为容器指定一个名字。
    - `my-springboot-app`：运行的镜像名称。

2. 检查容器是否正常运行：
   ```bash
   docker ps
   ```

3. 访问 Spring Boot 应用：
   在浏览器中访问 [http://localhost:8080](http://localhost:8080)（或应用暴露的其他端点）。

---

## **5. 调试和日志**
1. 查看容器日志：
   ```bash
   docker logs my-springboot-container
   ```

2. 进入容器内部进行调试：
   ```bash
   docker exec -it my-springboot-container /bin/bash
   ```

---

## **6. 停止和删除容器**
1. 停止容器：
   ```bash
   docker stop my-springboot-container
   ```

2. 删除容器：
   ```bash
   docker rm my-springboot-container
   ```

3. 删除镜像：
   ```bash
   docker rmi my-springboot-app
   ```

---

## **7. 使用 Docker Compose（可选）**
如果需要更复杂的部署（如依赖数据库），可以使用 Docker Compose。

### **7.1 创建 `docker-compose.yml`**
在项目根目录下创建 `docker-compose.yml`：

```yaml
version: '3.8'
services:
  app:
    image: my-springboot-app
    build:
      context: .
    ports:
      - "8080:8080"
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
    ports:
      - "3306:3306"
```

### **7.2 启动服务**
运行以下命令启动应用和数据库：
```bash
docker-compose up -d
```

---

## **总结**
以上步骤将 Spring Boot 程序打包并通过 Docker 部署。借助 Docker Compose，还可以轻松地集成数据库和其他服务，适合多环境开发和生产部署。
