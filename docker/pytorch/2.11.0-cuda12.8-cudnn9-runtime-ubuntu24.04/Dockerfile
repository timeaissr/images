# ==========================================
# Stage 1: Builder (后厨阶段 - 专心构建 Jupyter)
# ==========================================
FROM pytorch/pytorch:2.11.0-cuda12.8-cudnn9-runtime AS builder

# 将 jupyterlab 及其依赖安装到临时目录 /jupyter_build
RUN pip install --no-cache-dir --prefix=/jupyter_build jupyterlab


# ==========================================
# Stage 2: Final (前厅阶段 - 最终运行环境)
# ==========================================
FROM pytorch/pytorch:2.11.0-cuda12.8-cudnn9-runtime

# 1. 设置时区环境变量
ENV TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive

# 2. 安装系统级依赖：SSH、时区工具、语言包
RUN apt-get update && apt-get install -y --no-install-recommends \
    openssh-server \
    tzdata \
    locales \
    # 配置时区为 Asia/Shanghai
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    # 生成 en_US.UTF-8 语言环境
    && locale-gen en_US.UTF-8 \
    # 清理 apt 缓存
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# 3. 设置语言环境变量
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# 4. 配置 SSH
# (移除了 chpasswd 步骤，密码/密钥由算力服务器平台接管)
RUN mkdir /var/run/sshd \
    # 允许 root 登录 (服务端配置)
    && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
    # 取消严格主机密钥检查 (客户端配置，方便 git clone)
    && echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config

# 5. 注入应用级软件：从 builder 复制 JupyterLab
COPY --from=builder /jupyter_build /usr/local/

# 6. 暴露端口与设置工作目录
EXPOSE 22 8888
WORKDIR /workspace

# 7. 启动命令：只启动 SSH 服务，保持在前台运行
CMD ["/usr/sbin/sshd", "-D"]