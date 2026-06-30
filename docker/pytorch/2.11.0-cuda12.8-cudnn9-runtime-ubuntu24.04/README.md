# PyTorch 2.11.0 CUDA 12.8 GPU 深度学习运行环境镜像

`ghcr.io/timeaissr/pytorch:2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04` 是一个为深度学习和 AI 模型开发定制的高性能 GPU 运行环境镜像。它基于官方的 PyTorch 运行时镜像构建，并针对远程开发和云端算力平台优化了 SSH 连接。

---

## 📦 包含的软件与工具

* **深度学习核心框架**：
  * `PyTorch 2.11.0`
  * `CUDA 12.8`
  * `cuDNN 9`
  * 基础操作系统：`Ubuntu 24.04 LTS`
* **Python 包管理**：`uv`（由 ghcr.io/astral-sh/uv 官方镜像复制，替代 pip 管理 Python 依赖）
* **系统服务**：`openssh-server`（提供安全便捷的 SSH 远程连接与调试）
* **系统包与本地化**：`tzdata`（时区）、`locales`（语言环境）

---

## ⚙️ 核心配置特性

1. **远程 SSH 支持**：
   * **`22` 端口**：SSH 服务默认在容器启动时开启。
   * 允许 Root 用户通过 SSH 直接登录（`PermitRootLogin yes`）。
   * 关闭了客户端的严格主机密钥检查（`StrictHostKeyChecking no`），方便在多卡/多机分布式训练时快速配置 SSH 互信。
2. **时区与语言本地化**：
   * 预设时区：**`Asia/Shanghai`** (北京时间)。
   * 生成并配置 `en_US.UTF-8` 语言环境，规避英文字符集乱码问题。
3. **工作目录**：
   * 默认工作目录锁定为 `/workspace`。

---

## 🚀 快速启动与使用

### 1. 拉取镜像
```bash
docker pull ghcr.io/timeaissr/pytorch:2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04
```

### 2. 启动容器（带 GPU 支持与端口映射）
请确保宿主机已安装 `nvidia-container-toolkit`。运行以下命令启动容器，并映射 SSH 端口：
```bash
docker run -d --gpus all \
  --name pytorch-gpu-dev \
  -p 2225:22 \
  -v /path/to/your/code:/workspace \
  ghcr.io/timeaissr/pytorch:2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04
```

### 3. 使用 SSH 连接
由于默认构建镜像未指定 Root 静态密码，在常规运行或云算力平台上可通过挂载密钥或由算力平台注入密码进行远程连通：
```bash
ssh root@<Your-Host-IP> -p 2225
```

---

## 📂 Dockerfile 源码参考

镜像的构建逻辑可参考同目录下的 [Dockerfile](Dockerfile)。
