---
title: "Docker和Docker Compose的安装"
description: "Docker和Docker Compose的安装"
publishDate: "29 Mar 2023"
tags: ["test", "toc"]
---

## Docker和Docker Compose的安装

### 使用官方源安装，在root用户下进行

```shell
apt update
apt upgrade -y
apt install curl vim wget gnupg dpkg apt-transport-https lsb-release ca-certificates
```

加入docker的GPG公钥和apt源：

```shell
curl -sSL https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://download.docker.com/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list
```

然后更新系统后即可安装docker CE:

```shell
apt update
apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

此时用`docker version`命令检查是否安装成功：

```shell
root@debian ~ # docker version
Client: Docker Engine - Community
 Version:           20.10.22
 API version:       1.41
 Go version:        go1.18.9
 Git commit:        3a2c30b
 Built:             Thu Dec 15 22:28:22 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.22
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.9
  Git commit:       42c8b31
  Built:            Thu Dec 15 22:26:14 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.14
  GitCommit:        9ba4b250366a5ddde94bb7c9d1def331423aa323
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

如果需要某个特定用户可以用 Docker [rootless](https://docs.docker.com/engine/security/rootless/) 模式运行 Docker，那么可以把这个用户也加入 docker 组，比如我们把 `www-data` 用户加进去：

```shell
apt install docker-ce-rootless-extras
sudo usermod -aG docker www-data
```

### 安装Docker Compose

因为我们已经安装了 `docker-compose-plugin`，所以 Docker 目前已经自带 `docker compose` 命令，基本上可以替代 `docker-compose`：

```shell
root@debian ~ # docker compose version
Docker Compose version v2.14.1
```

如果某些镜像或命令不兼容，则我们还可以单独安装 Docker Compose：

我们可以使用 Docker 官方发布的 [Github](https://github.com/docker/compose) 直接安装最新版本：

```shell
curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-Linux-x86_64 > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

此时可以使用 `docker-compose version` 命令检查是否安装成功：

```shell
root@debian ~ # docker-compose version
Docker Compose version v2.14.2
```

### 修改Docker配置

以下配置会增加一段自定义内网 IPv6 地址，开启容器的 IPv6 功能，以及限制日志文件大小，防止 Docker 日志塞满硬盘（泪的教训）：

```shell
cat > /etc/docker/daemon.json << EOF
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "20m",
        "max-file": "3"
    },
    "ipv6": true,
    "fixed-cidr-v6": "fd00:dead:beef:c0::/80",
    "experimental":true,
    "ip6tables":true
}
EOF
```

然后重启Docker服务：

```shell
systemctl restart docker
```

