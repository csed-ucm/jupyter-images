# Tinygrad + JupyterHub + CUDA 13.0 on Ubuntu 24.04
FROM nvidia/cuda:13.0.2-devel-ubuntu24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/usr/local/cuda/bin:${PATH}"
ENV LD_LIBRARY_PATH="/usr/local/cuda/lib64:${LD_LIBRARY_PATH}"
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# --- System setup ---
RUN apt-get update && apt-get install -y --no-install-recommends \
    git wget ca-certificates build-essential \
    python3 python3-pip python3-dev python3-venv clang sudo \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

# --- Create non-root user safely ---
ARG NB_USER=jovyan
ARG NB_UID=1001
ARG NB_GID=1001
ENV USER=${NB_USER} \
    HOME=/home/${NB_USER}

RUN groupadd --gid ${NB_GID} ${NB_USER} && \
    useradd --uid ${NB_UID} --gid ${NB_GID} -m -s /bin/bash ${NB_USER} && \
    echo "${NB_USER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# --- Create venv owned by the non-root user ---
RUN python3 -m venv /opt/venv && chown -R ${NB_USER}:${NB_USER} /opt/venv
ENV PATH="/opt/venv/bin:${PATH}"

# --- Upgrade pip and install dependencies ---
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# --- Install Jupyter stack ---
RUN pip install --no-cache-dir jupyterhub jupyterlab notebook

# --- tinygrad setup ---
RUN git clone https://github.com/geohot/tinygrad.git /opt/tinygrad && \
    chown -R ${NB_USER}:${NB_USER} /opt/tinygrad

USER ${NB_USER}
WORKDIR /opt/tinygrad

RUN pip install --no-cache-dir -e .

# --- Optional PyTorch (may not have cu130 wheel yet) ---
RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cu130 || \
    echo "⚠️  PyTorch cu130 wheel unavailable; continuing without it."

# --- Default working directory ---
WORKDIR ${HOME}

# --- Launch JupyterLab by default (override with CMD in Hub) ---
CMD ["jupyter-lab", "--ip=0.0.0.0", "--no-browser"]
