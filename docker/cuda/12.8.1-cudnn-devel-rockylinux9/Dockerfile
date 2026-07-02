FROM nvcr.io/nvidia/cuda:12.8.1-cudnn-devel-rockylinux9

ENV TZ=Asia/Shanghai

# 安装必要工具并配置 SSH、时区和语言环境
# Rocky Linux 9 基础镜像已预装 curl-minimal, tzdata, ca-certificates
# 仅安装额外需要的 openssh-server, glibc-langpack-en, wget
RUN dnf install -y --setopt=tsflags=nodocs \
        openssh-server \
        glibc-langpack-en \
        wget \
    && dnf clean all \
    && rm -rf /var/cache/dnf \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && ssh-keygen -A \
    && mkdir -p /var/run/sshd \
    && sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config

ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

EXPOSE 22
WORKDIR /workspace

CMD ["/usr/sbin/sshd", "-D"]
