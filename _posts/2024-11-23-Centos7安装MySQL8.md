---
title: "Centos7安装MySQL8"
date: 2024-11-23
---

## 介绍

在Centos7电脑操作系统安装MySQL8

## 1 下载MySQL8

### 1.1 进入下载页面

https://dev.mysql.com/downloads/mysql/

### 1.2 查看系统的`glibc`版本

```bash
[root@localhost ~]# rpm -qa | grep glibc
glibc-common-2.17-326.el7_9.3.x86_64
glibc-headers-2.17-326.el7_9.3.x86_64
glibc-2.17-326.el7_9.3.i686
glibc-2.17-326.el7_9.3.x86_64
glibc-devel-2.17-326.el7_9.3.x86_64
```

这里的版本是glibc是2.17，所以在下载安装包的时候，需要选择glibc2.17安装包

### 1.3 选择下载版本

![image-20241123102709710]({{ site.baseurl }}/assets/2024-11-23-Centos7安装MySQL8.assets/image-20241123102709710.png)

## 2 MySQL8安装

执行下面命令进行安装

```bash
$> groupadd mysql
$> useradd -r -g mysql -s /bin/false mysql
$> cd /usr/local
$> tar xvf /path/to/mysql-VERSION-OS.tar.xz
$> ln -s full-path-to-mysql-VERSION-OS mysql
$> cd mysql
$> mkdir mysql-files
$> chown mysql:mysql mysql-files
$> chmod 750 mysql-files
$> bin/mysqld --initialize --user=mysql
$> bin/mysqld_safe --user=mysql &
# Next command is optional
$> cp support-files/mysql.server /etc/init.d/mysql.server
```

## 3 MySQL命令

### 3.1 启动MySQL

```bash
/usr/local/mysql/bin/mysqld_safe --user=mysql &
```

### 3.2 停止MySQL

```bash
/usr/local/mysql/bin/mysqladmin -u root -p shutdown
```

回车后，输入MySQL密码

### 3.3 查看MySQL版本

```bash
/usr/local/mysql/bin/mysqladmin -u root -p version
```

回车后，输入MySQL密码

### 3.4 查看MySQL配置项

```bash
/usr/local/mysql/bin/mysqladmin -u root -p variables
```

回车后，输入MySQL密码

### 3.5 导出数据库

```bash
#包含 --databases
/usr/local/mysql/bin/mysqldump -h localhost -u root -p -P 3303 --databases bb > bb.sql
#不包含 --databases
/usr/local/mysql/bin/mysqldump -h localhost -u root -p -P 3303 bb > bb.sql
```

- 包含 `--databases`，会包含创建数据库语句
- 不包含 `--databases`，不会创建数据库语句

### 3.6 登录数据库

```bash
/usr/local/mysql/bin/mysql -u root -p -h 127.0.0.1 -P 3303
```

### 3.7 导入数据库

#### 3.7.1 不登录导入，不指定数据库名称

如果导出时导出命令包含`--databases`可以使用，或者导入全部数据库时可以使用

```bash
/usr/local/mysql/bin/mysql < dump.sql
```

### 3.7.2 不登录导入，指定数据库名称

```bash
/usr/local/mysql/bin/mysql db1 < dump.sql
```

### 3.7.3 登录导入

```mysql
mysql> source dump.sql
```

