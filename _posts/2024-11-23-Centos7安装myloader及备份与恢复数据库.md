---
title: "Centos7安装myloader及备份与恢复数据库"
date: 2024-11-23
---

## 1 介绍

MyDumper 是一个 MySQL 逻辑备份工具。它有 2 个工具：

- mydumper，负责导出 MySQL 数据库的一致备份
- myloader 从 mydumper 读取备份，连接到目标数据库并导入备份。

这两个工具都使用多线程功能。MyDumper 是开源的，由社区维护，它不是 Percona、MariaDB 或 MySQL 产品。

## 2 安装

```bash
wget -O GPG-KEY-MyDumper "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x79EA15C0E82E34BA"
rpm --import GPG-KEY-MyDumper
```

创建文件：`vim /etc/yum.repos.d/mydumper.repo`，写入下面内容。

```bash
[mydumper]
name=MyDumper
baseurl=https://mydumper.github.io/mydumper/repo/yum/
enabled=1
gpgcheck=1

[mydumper-testing]
name=MyDumper
baseurl=https://mydumper.github.io/mydumper/repo/yum/testing/
enabled=0
gpgcheck=1
```

## 3 备份与恢复数据库

### 3.1 备份数据库

```bash
mydumper -u root -p 666666 -h 127.0.0.1 -P 3306 -B dbnasme -o /data/backup -c --clear
```

选项说明：

- -u：数据库用户名
- -p：数据库密码
- -h：数据库服务器ip地址或域名
- -P：数据库服务器端口
- -B：备份数据库名称
- -o：备份文件输出目录
- -c：备份文件压缩
- --clear：备份时清空输出目录的所有文件

### 3.2 恢复备份

```bash
myloader -d /data/backup -u root -p 666666 -h 127.0.0.1 -P 3306 -B aa
```

选项说明：

- -d：备份文件把目录
- -u：数据库用户名
- -p：数据库密码
- -h：数据库服务器ip地址或域名
- -P：数据库服务器端口
- -B：备份数据库名称

### 3.3 备份问题

备份时可能会出现数据库用户没有备份权限错误。这时需要登录mysql数据库，赋予用户权限，执行下面命令：

**授权**

```mysql
grant BACKUP_ADMIN on *.* to 'root'@'%';
```

**刷新**

```mysql
FLUSH PRIVILEGES;
```





