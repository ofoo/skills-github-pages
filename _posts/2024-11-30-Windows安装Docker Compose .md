---
title: "Windows安装Docker Compose"
date: 2024-11-30
---





## 安装步骤

1 以管理员身份运行 PowerShell。当系统询问您是否允许此应用更改您的设备时，选择**“是”**以继续安装。

2 GitHub 现在需要 TLS1.2。在 PowerShell 中，运行以下命令：

```bash
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

3 运行以下命令下载最新版本的Compose（v2.30.3）：

```bash
 Start-BitsTransfer -Source "https://github.com/docker/compose/releases/download/v2.30.3/docker-compose-windows-x86_64.exe" -Destination $Env:ProgramFiles\Docker\docker-compose.exe
```

要安装不同版本的 Compose，`v2.30.3`请用您想要使用的 Compose 版本替换。

4 测试安装。

```bash
docker-compose.exe version
Docker Compose version v2.30.3
```

