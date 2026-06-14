# PyTorch 2.11.0 CUDA 12.8 GPU 深度学习运行环境镜像

`ghcr.io/timeaissr/pytorch:2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04` 是一个为深度学习和 AI 模型开发定制的高性能 GPU 运行环境镜像。它基于官方的 PyTorch 运行时镜像构建，集成了交互式开发利器 JupyterLab，并针对远程开发和云端算力平台优化了 SSH 连接。

---

## 📦 包含的软件与工具

* **深度学习核心框架**：
  * `PyTorch 2.11.0`
  * `CUDA 12.8`
  * `cuDNN 9`
  * 基础操作系统：`Ubuntu 24.04 LTS`
* **开发与交互式工具**：
  * `JupyterLab`（通过多阶段构建在临时环境中安装并提纯，直接注入到运行环境中，保持镜像整洁且体积优化）
* **系统服务**：`openssh-server`（提供安全便捷的 SSH 远程连接与调试）
* **系统包与本地化**：`tzdata`（时区）、`locales`（语言环境）

---

## ⚙️ 核心配置特性

1. **双暴露端口支持**：
   * **`22` 端口**：SSH 服务默认在容器启动时开启。
   * **`8888` 端口**：JupyterLab 服务交互端口。
2. **远程 SSH 支持**：
   * 允许 Root 用户通过 SSH 直接登录（`PermitRootLogin yes`）。
   * 关闭了客户端的严格主机密钥检查（`StrictHostKeyChecking no`），方便在多卡/多机分布式训练时快速配置 SSH 互信。
3. **时区与语言本地化**：
   * 预设时区：**`Asia/Shanghai`** (北京时间)。
   * 生成并配置 `en_US.UTF-8` 语言环境，规避英文字符集乱码问题。
4. **工作目录**：
   * 默认工作目录锁定为 `/workspace`。

---

## 🚀 快速启动与使用

### 1. 拉取镜像
```bash
docker pull ghcr.io/timeaissr/pytorch:2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04
```

### 2. 启动容器（带 GPU 支持与端口映射）
请确保宿主机已安装 `nvidia-container-toolkit`。运行以下命令启动容器，并映射 SSH (2225) 和 JupyterLab (8889) 端口：
```bash
docker run -d --gpus all \
  --name pytorch-gpu-dev \
  -p 2225:22 \
  -p 8889:8888 \
  -v /path/to/your/code:/workspace \
  ghcr.io/timeaissr/pytorch:2.11.0-cuda12.8-cudnn9-runtime-ubuntu24.04
```

### 3. 连接与交互开发

#### A. 使用 SSH 登录
由于默认构建镜像未指定 Root 静态密码，在常规运行或云算力平台上可通过挂载密钥或由算力平台注入密码进行远程连通：
```bash
ssh root@<Your-Host-IP> -p 2225
```

#### B. 启动与使用 JupyterLab
默认容器启动命令为 `sshd -D`（保持 SSH 在前台运行）。如果您需要使用 JupyterLab，可以进入容器执行以下命令：
```bash
# 启动 JupyterLab 服务
jupyter lab --ip=0.0.0.0 --port=8888 --allow-root --no-browser --NotebookApp.token='your_custom_token'
```
随后，在宿主机浏览器中访问：`http://localhost:8889/?token=your_custom_token` 即可开始编码。

---

## 📂 Dockerfile 源码参考

镜像的构建逻辑可参考同目录下的 [Dockerfile](Dockerfile)。
