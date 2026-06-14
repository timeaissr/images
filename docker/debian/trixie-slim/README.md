# Debian 13 Slim (Trixie-Slim) 极简连接镜像

`ghcr.io/timeaissr/debian:trixie-slim` (或 `ghcr.io/timeaissr/debian:13-slim`) 是一个基于 Debian 13 Slim 镜像构建的轻量级连接镜像。该镜像在保障 SSH 远程直连与核心网络请求功能的同时，尽量精简了体积，非常适合作为基础沙箱或在此之上自定义构建更复杂的应用镜像。

---

## 📦 包含的软件与工具

本镜像只保留了建立远程连接和进行基础网络通信所需的最低限度工具，以最大化压缩镜像体积：

* **系统服务**：`openssh-server`（用于 SSH 远程连通）
* **基础网络与证书**：`curl`、`wget`、`ca-certificates`（保障安全的 HTTPS 连接与网络资源下载）
* **本地化与时区支持**：`locales`、`tzdata`

---

## ⚙️ 核心配置特性

1. **远程 SSH 支持**：
   * SSH 服务默认开启，监听 `22` 端口。
   * 开启 Root 登录权限（`PermitRootLogin yes`）。
   * 关闭了客户端严格主机密钥检查（`StrictHostKeyChecking no`），确保自动化构建或跨机器连接时无阻碍。
2. **本地化与时区**：
   * 时区预设为 **`Asia/Shanghai`** (北京时间)。
   * 支持 `zh_CN.UTF-8` 和 `en_US.UTF-8` 双语言环境。
   * 默认语言指定为 `en_US.UTF-8`。

---

## 🚀 快速启动与使用

### 1. 拉取镜像
```bash
docker pull ghcr.io/timeaissr/debian:trixie-slim
```

### 2. 启动容器
后台启动容器并映射 SSH 的 `22` 端口：
```bash
docker run -d \
  --name debian-trixie-slim \
  -p 2223:22 \
  ghcr.io/timeaissr/debian:trixie-slim
```

### 3. 连接到容器
* **通过 SSH 登录**（默认未设置 root 默认静态密码，密码通常由算力云平台接管或在容器启动时动态注入）：
  ```bash
  ssh root@<Your-Host-IP> -p 2223
  ```
* **通过本地 Docker 终端直接进入**：
  ```bash
  docker exec -it debian-trixie-slim bash
  ```

---

## 📂 Dockerfile 源码参考

镜像的构建逻辑可参考同目录下的 [Dockerfile](Dockerfile)。
