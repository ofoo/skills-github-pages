---
title: "Centos7安装Percona XtraBackup 8.0"
date: 2024-11-23
---

## 介绍

在Centos7电脑操作系统安装Percona XtraBackup 8.0

## 1 下载安装包

```bash
wget https://downloads.percona.com/downloads/Percona-XtraBackup-8.0/Percona-XtraBackup-8.0.35-30/binary/tarball/percona-xtrabackup-8.0.35-30-Linux-x86_64.glibc2.17.tar.gz
```

## 2 解压安装包

```bash
tar -xvf percona-xtrabackup-8.0.35-30-Linux-x86_64.glibc2.17.tar.gz
```

进入bin目录：

```bash
cd percona-xtrabackup-8.0.35-30-Linux-x86_64.glibc2.17/bin
```

运行二进制文件：

```bash
./xtrabackup --version
```