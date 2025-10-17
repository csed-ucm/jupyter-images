# Dockerfile for tinygrad on JupyterHub with CUDA 13.0

# Step 1: Base Image with CUDA Development Toolkit
# CHANGED: Updated to CUDA 13.0.1 to match your node's driver capabilities.
# This ensures nvcc can generate compatible PTX code for your GPU.
FROM nvidia/cuda:13.0.1-devel-ubuntu22.04

# Step 2: Set up Environment
# Prevent interactive prompts during installation and configure CUDA paths.
ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/usr/local/cuda/bin:${PATH}"
ENV LD_LIBRARY_PATH="/usr/local/cuda/lib64:${LD_LIBRARY_PATH}"

# Step 3: Install System Dependencies, Python, and Jupyter
# We need git to clone tinygrad and Python to run it.
# Then, we install the necessary Jupyter components for JupyterHub.
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    wget \
    build-essential \
    python3.11 \
    python3-pip \
    python3-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip and install Jupyter components
RUN python3 -m pip install --no-cache-dir --upgrade pip
RUN python3 -m pip install --no-cache-dir jupyterhub jupyterlab notebook

# Step 4: Install tinygrad from Source
# Cloning the repository ensures we get the latest version.
RUN git clone https://github.com/geohot/tinygrad.git /opt/tinygrad
WORKDIR /opt/tinygrad
# Install in editable mode; this is good practice.
RUN python3 -m pip install --no-cache-dir -e .

# Step 5: (Optional) Install PyTorch for Verification
# CHANGED: Updated the PyTorch wheel to cu130 to align with the CUDA version.
RUN python3 -m pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cu130

# Step 6: Create a Non-Root User for Jupyter
# Running containers as a non-root user is a security best practice.
# 'jovyan' is the standard user for the Jupyter project.
ARG NB_USER=jovyan
ARG NB_UID=1000
ENV HOME="/home/${NB_USER}"
RUN adduser --disabled-password --gecos "Default user" --uid ${NB_UID} ${NB_USER}
WORKDIR ${HOME}
# Switch to the new user
USER ${NB_USER}

# Step 7: Define the Entrypoint for JupyterHub
# This command starts the single-user notebook server that JupyterHub
