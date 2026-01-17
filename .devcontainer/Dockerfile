FROM python:3.14.2-bookworm

LABEL maintainer="LouisLiu"
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        libpq-dev \
        libgomp1 \
        git \
        curl \
        wget \
        nano \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://astral.sh/uv/install.sh | sh

# 配置pip缓存+uv环境变量，加速所有依赖安装
ENV PIP_CACHE_DIR=/usr/local/pip-cache \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONIOENCODING=utf-8
RUN mkdir -p $PIP_CACHE_DIR

# 项目工作目录
WORKDIR /workspaces/CodeGuardian