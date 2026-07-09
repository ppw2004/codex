FROM ubuntu:24.04

# 接收构建传入的代理参数
ARG HTTP_PROXY
ARG HTTPS_PROXY
ARG ALL_PROXY
ARG NO_PROXY

# 同时设置大写+小写环境变量，适配 curl/wget/rustup/apt/pip
ENV HTTP_PROXY=${HTTP_PROXY}
ENV HTTPS_PROXY=${HTTPS_PROXY}
ENV ALL_PROXY=${ALL_PROXY}
ENV NO_PROXY=${NO_PROXY}
ENV http_proxy=${HTTP_PROXY}
ENV https_proxy=${HTTPS_PROXY}
ENV all_proxy=${ALL_PROXY}
ENV no_proxy=${NO_PROXY}

# apt 代理配置
RUN echo 'Acquire::http::Proxy "'${HTTP_PROXY}'";' > /etc/apt/apt.conf.d/99proxy && \
    echo 'Acquire::https::Proxy "'${HTTPS_PROXY}'";' >> /etc/apt/apt.conf.d/99proxy

# 后面你的原有安装逻辑、rustup 安装...

ARG DEBIAN_FRONTEND=noninteractive
# enable 'universe' because musl-tools & clang live there
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    software-properties-common && \
    add-apt-repository --yes universe

# now install build deps
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    build-essential curl git ca-certificates \
    pkg-config libcap-dev clang musl-tools libssl-dev just && \
    rm -rf /var/lib/apt/lists/*

# Ubuntu 24.04 ships with user 'ubuntu' already created with UID 1000.
USER ubuntu

# install Rust + musl target as dev user
RUN curl -sSf https://sh.rustup.rs | sh -s -- -y --profile minimal && \
    ~/.cargo/bin/rustup target add aarch64-unknown-linux-musl && \
    ~/.cargo/bin/rustup component add clippy rustfmt

ENV PATH="/home/ubuntu/.cargo/bin:${PATH}"

WORKDIR /workspace
