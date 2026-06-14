# Debian 13 (Trixie) 全功能开发镜像

`ghcr.io/timeaissr/debian:trixie` (或 `ghcr.io/timeaissr/debian:13`) 是一个基于 Debian 13 (Trixie) 构建的全功能 Linux 开发环境镜像。它为云端开发、算力平台远程工作以及日常容器化研发提供了开箱即用的基础环境。

---

## 📦 包含的软件与工具

本镜像预装了完整的开发常用工具链，无需在容器启动后重复配置：

* **系统服务**：`openssh-server`（提供安全便捷的 SSH 远程连接）
* **编译工具链**：`build-essential`（包含 `gcc`、`g++`、`make`、`dpkg-dev` 等）
* **基础网络工具**：`curl`、`wget`
* **版本控制**：`git`
* **文本编辑**：`vim`
* **本地化与时区支持**：`locales`、`tzdata`

---

## ⚙️ 核心配置特性

1. **远程 SSH 支持**：
   * SSH 服务默认开启，监听 `22` 端口。
   * 支持 Root 账户通过 SSH 直接登录（`PermitRootLogin yes`）。
   * 关闭了客户端的严格主机密钥检查（`StrictHostKeyChecking no`），方便在多机集群/算力平台上无密码 Git 拉取或互信。
2. **时区与语言本地化**：
   * 系统时区已锁定为 **`Asia/Shanghai`** (北京时间)。
   * 生成并配置了双语言环境支持：`zh_CN.UTF-8` 和 `en_US.UTF-8`。
   * 系统默认语言设置为 `en_US.UTF-8`，彻底避免终端输出中文乱码。

---

## 🚀 快速启动与使用

### 1. 拉取镜像
```bash
docker pull ghcr.io/timeaissr/debian:trixie
```

### 2. 启动容器
后台运行容器，并将容器的 `22` (SSH) 端口映射到宿主机的 `2222` 端口：
```bash
docker run -d \
  --name debian-trixie-dev \
  -p 2222:22 \
  ghcr.io/timeaissr/debian:trixie
```

### 3. 连接到容器
* **通过 SSH 登录**（适用于算力云平台接管或远程直连，请确保已通过密钥/平台机制配置密码，默认构建未设置 root 默认静态密码，密码由平台或启动脚本注入）：
  ```bash
  ssh root@<Your-Host-IP> -p 2222
  ```
* **通过本地 Docker 终端直接进入**：
  ```bash
  docker exec -it debian-trixie-dev bash
  ```

---

## 📂 Dockerfile 源码参考

镜像的构建逻辑可参考同目录下的 [Dockerfile](Dockerfile)。
