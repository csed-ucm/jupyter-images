# Dockerfile for tinygrad on JupyterHub with CUDA 13.0 using a virtual environment

FROM nvidia/cuda:13.0.2-devel-ubuntu24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/usr/local/cuda/bin:${PATH}"
ENV LD_LIBRARY_PATH="/usr/local/cuda/lib64:${LD_LIBRARY_PATH}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    wget \
    ca-certificates \
    build-essential \
    python3 \
    python3-pip \
    python3-dev \
    python3-venv \
    clang \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Create and activate virtual environment
RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:${PATH}"
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Upgrade pip inside venv safely
RUN pip install --no-cache-dir --upgrade pip

# Install Jupyter stack
RUN pip install --no-cache-dir jupyterhub jupyterlab notebook

# tinygrad
RUN git clone https://github.com/geohot/tinygrad.git /opt/tinygrad
WORKDIR /opt/tinygrad
RUN pip install --no-cache-dir -e .

# Optional PyTorch (may fail if cu130 wheels not published yet)
RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cu130 || \
    (echo "WARNING: PyTorch cu130 wheel not found yet; continuing without torch" && true)

# Non-root user
ARG NB_USER=jovyan
ARG NB_UID=1000
ENV HOME="/home/${NB_USER}"
RUN id -u ${NB_USER} >/dev/null 2>&1 || adduser --disabled-password --gecos "Default user" --uid ${NB_UID} ${NB_USER}

WORKDIR ${HOME}
USER ${NB_USER}
