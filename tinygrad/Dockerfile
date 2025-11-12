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

USER root
# Detect existing UID 1000 user (usually "ubuntu" or "user") and rename it to jovyan
RUN EXISTING_USER=$(getent passwd 1000 | cut -d: -f1) && \
    if [ -n "$EXISTING_USER" ]; then \
        echo "Renaming existing UID 1000 user: $EXISTING_USER -> jovyan"; \
        usermod -l jovyan -d /home/jovyan -m "$EXISTING_USER"; \
        groupmod -n jovyan "$EXISTING_USER" || true; \
    else \
        echo "No existing UID 1000 user found, creating jovyan"; \
        useradd -m -s /bin/bash -u 1000 jovyan; \
    fi && \
    # ensure group exists and home ownership is correct
    chown -R jovyan:$(id -gn jovyan) /home/jovyan

USER jovyan

WORKDIR ${HOME}
USER ${NB_USER}
