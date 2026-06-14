# images

这是一个基于 GitHub Actions 自动构建并托管于 GitHub Container Registry (GHCR) 的自定义 Docker 镜像库。项目旨在为云算力平台和日常开发提供开箱即用、配置完善的容器环境。

---

## 🌐 镜像托管信息

所有镜像均发布至 GitHub Container Registry (GHCR)：
* **注册表地址**：`ghcr.io`
* **命名空间**：`timeaissr`

---

## 📦 已包含的镜像列表

### 1. Debian 开发镜像系列 (`ghcr.io/timeaissr/debian`)
基于 Debian 13 (Trixie) 构建，针对网络、时区、中文支持和远程连接进行了深度优化。

| 标签 (Tag) | 基础镜像 | 预装主要软件 / 功能特点 | 适用场景 |
| :--- | :--- | :--- | :--- |
| [`trixie`](docker/debian/trixie/README.md), `13` | `debian:trixie` | OpenSSH Server, build-essential, git, wget, curl, vim, zip, 时区/中文语言包 | 全功能轻量级开发环境，适合 C++/Git 常用开发 |
| [`trixie-slim`](docker/debian/trixie-slim/README.md), `13-slim` | `debian:trixie-slim` | OpenSSH Server, wget, curl, ca-certificates, 时区/中文语言包 | 极简连接镜像，适合仅需 SSH 连接和基础网络工具的场景 |
| [`trixie-slim-miniforge`](docker/debian/trixie-slim-miniforge/README.md), `13-slim-miniforge`, `trixie-slim-mf`, `13-slim-mf` | `debian:trixie-slim` | Miniforge3 (conda & mamba), OpenSSH Server, wget, curl, ca-certificates, 基础包 | Python / Data Science 极简沙箱与环境依赖隔离 |

### 2. PyTorch 深度学习镜像系列 (`ghcr.io/timeaissr/pytorch`)
基于官方 PyTorch 镜像构建，并集成了交互式开发工具与远程管理工具。

| 标签 (Tag) | 基础镜像 | 预装主要软件 / 功能特点 | 适用场景 |
| :--- | :--- | :--- | :--- |
| [`2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04`](docker/pytorch/2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04/README.md) | `pytorch/pytorch:2.11.0-cuda12.8-cudnn9-runtime` | JupyterLab, OpenSSH Server, 时区(Asia/Shanghai), 语言包(en_US.UTF-8) | GPU 算力平台、深度学习模型训练与交互式调试 |

---

## 🛠️ 镜像通用配置特性

所有镜像在构建时均包含以下通用特性，以保障在各大 GPU 云平台或容器托管环境下的易用性：

1. **远程 SSH 支持**：
   * 预装并配置了 `openssh-server`。
   * 允许 Root 登录（`PermitRootLogin yes`），且关闭了客户端的严格主机密钥检查（`StrictHostKeyChecking no`），方便集群互联。
   * 默认暴露端口：`22` (SSH) 与 `8888` (JupyterLab，如果存在)。
2. **本地化与时区**：
   * 时区统一配置为 `Asia/Shanghai` (北京时间)。
   * 生成并支持 `en_US.UTF-8` 和 `zh_CN.UTF-8` 编码，避免终端中文乱码。
3. **极速构建 & 镜像体积优化**：
   * 采用多阶段构建 (Multi-stage build) 提炼软件依赖（如 JupyterLab 的安装），确保最终镜像体积精简。
   * 自动清理 `apt` 缓存和临时构建产物。

---

## 🚀 快速使用指南

### 1. 拉取镜像
```bash
# 拉取 Debian 13 开发环境镜像
docker pull ghcr.io/timeaissr/debian:trixie

# 拉取 PyTorch 2.11.0 深度学习镜像
docker pull ghcr.io/timeaissr/pytorch:2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04
```

### 2. 运行并连接容器
通过以下命令运行容器并映射 SSH 端口：
```bash
docker run -d \
  --name my-dev-container \
  -p 2222:22 \
  -p 8888:8888 \
  ghcr.io/timeaissr/debian:trixie
```

---

## 📂 项目目录结构

```text
.
├── .github/
│   └── workflows/              # GitHub Actions 工作流定义 (自动触发构建与发布)
├── docker/
│   ├── debian/
│   │   ├── trixie/                 # Debian Trixie 全功能开发环境
│   │   ├── trixie-slim/            # Debian Trixie 极简连接镜像
│   │   └── trixie-slim-miniforge/  # Debian Trixie + Miniforge (Conda) 环境
│   └── pytorch/
│       └── 2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04/
│                                   # PyTorch 运行环境 + JupyterLab
├── .gitignore
├── LICENSE
└── README.md
```

---

## 🔄 自动化构建流程

项目利用 GitHub Actions 进行持续集成与部署：
1. **触发条件**：当推送到 `main` 分支且对应镜像目录或工作流定义文件发生变更时，自动触发构建。
2. **构建平台**：基于 `ubuntu-latest` 运行器，自动登录 GHCR，完成镜像的构建与打标并进行推送。
3. **标签策略**：
   * 提供版本/环境语义标签（如 `trixie-slim-miniforge` / `2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04`）。
   * 提供带 Git SHA 的唯一性构建标签，便于追踪与回滚。
