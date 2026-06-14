# Debian 13 Slim + Miniforge 镜像

`ghcr.io/timeaissr/debian:trixie-slim-miniforge` (或其它别名如 `13-slim-miniforge`、`trixie-slim-mf`、`13-slim-mf`) 是在 Debian 13 Slim 极简连接镜像的基础上，预装并初始化了最新版 Miniforge3 的容器环境。它为 Python 开发、数据科学沙箱及虚拟环境隔离提供了轻量化、速度极快的 Mamba/Conda 支持。

---

## 📦 包含的软件与工具

* **虚拟环境管理器**：`Miniforge3`（预装在 `/opt/miniforge3` 目录，自带高效的包管理工具 `mamba` 与 `conda`）
* **系统服务**：`openssh-server`（用于远程 SSH 直连）
* **基础辅助工具**：`ca-certificates`、`curl`、`wget`、`gnupg`、`bzip2`（Conda 安装环境依赖）
* **本地化与时区支持**：`locales`、`tzdata`

---

## ⚙️ 核心配置特性

1. **Miniforge/Conda 配置**：
   * Conda 的默认安装和执行路径为 `/opt/miniforge3/bin`，已自动追加至系统环境变量 `$PATH`。
   * 已完成 Shell 初始化（`conda init bash`），登录容器后即可直接执行 `conda` 或 `mamba`。
   * 预设 Conda 属性：自动确认操作（`always_yes yes`）且不强制修改命令行提示符的前缀（`changeps1 no`）。
2. **远程 SSH 支持**：
   * SSH 服务默认在容器启动时开启（监听 `22` 端口）。
   * 允许 Root 登录（`PermitRootLogin yes`），屏蔽严格主机密钥检查（`StrictHostKeyChecking no`）。
3. **本地化与时区**：
   * 预设时区：**`Asia/Shanghai`** (北京时间)。
   * 默认语言环境：`en_US.UTF-8`。

---

## 🚀 快速启动与使用

### 1. 拉取镜像
```bash
docker pull ghcr.io/timeaissr/debian:trixie-slim-miniforge
```

### 2. 启动容器
后台启动容器并映射 SSH 端口：
```bash
docker run -d \
  --name debian-miniforge \
  -p 2224:22 \
  ghcr.io/timeaissr/debian:trixie-slim-miniforge
```

### 3. 连接并操作 Conda
* **进入容器终端**：
  ```bash
  docker exec -it debian-miniforge bash
  ```
* **创建新的虚拟环境并安装 Python 依赖（推荐使用极速安装工具 mamba）**：
  ```bash
  # 创建一个 Python 3.10 环境
  mamba create -n myenv python=3.10

  # 激活环境
  conda activate myenv

  # 使用 mamba 极速安装科学计算包
  mamba install numpy pandas matplotlib
  ```

---

## 📂 Dockerfile 源码参考

镜像的构建逻辑可参考同目录下的 [Dockerfile](Dockerfile)。
