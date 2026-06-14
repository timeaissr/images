# /docker/debian/trixie-slim/Dockerfile
FROM debian:trixie-slim

ENV DEBIAN_FRONTEND=noninteractive

# 仅安装维持云平台连接的"必要工具"
RUN apt-get update && apt-get install -y \
      openssh-server \
      locales \
      tzdata \
      wget \
      curl \
      ca-certificates \
      && rm -rf /var/lib/apt/lists/*

# 配置 SSH (供云平台直连)
RUN mkdir -p /var/run/sshd && \
      sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config && \
      cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new && \
      echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new && \
      mv /etc/ssh/ssh_config.new /etc/ssh/ssh_config

# 设置语言和时区
RUN locale-gen zh_CN.UTF-8 en_US.UTF-8 && \
      cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
      echo 'Asia/Shanghai' >/etc/timezone
ENV LANG=en_US.UTF-8 LANGUAGE=en_US:en LC_ALL=en_US.UTF-8

WORKDIR /root
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]