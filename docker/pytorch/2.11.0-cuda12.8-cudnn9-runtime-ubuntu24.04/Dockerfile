FROM pytorch/pytorch:2.11.0-cuda12.8-cudnn9-runtime

ENV TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    openssh-server \
    tzdata \
    locales \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && locale-gen en_US.UTF-8 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

RUN mkdir /var/run/sshd \
    && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

EXPOSE 22
WORKDIR /workspace

CMD ["/usr/sbin/sshd", "-D"]
