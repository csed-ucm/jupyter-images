# Dockerfile for tinygrad on JupyterHub with CUDA 13.0

# Step 1: Base Image with CUDA Development Toolkit
# CHANGED: Updated to CUDA 13.0.1 to match your node's driver capabilities.
# This ensures nvcc can generate compatible PTX code for your GPU.
[cite_start]FROM nvidia/cuda:13.0.1-devel-ubuntu22.04 [cite: 3]

# Step 2: Set up Environment
# Prevent interactive prompts during installation and configure CUDA paths.
[cite_start]ENV DEBIAN_FRONTEND=noninteractive [cite: 4]
ENV PATH="/usr/local/cuda/bin:${PATH}"
ENV LD_LIBRARY_PATH="/usr/local/cuda/lib64:${LD_LIBRARY_PATH}"

# Step 3: Install System Dependencies, Python, and Jupyter
# We need git to clone tinygrad and Python to run it.
# [cite_start]Then, we install the necessary Jupyter components for JupyterHub. [cite: 5]

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    wget \
    build-essential \
    python3.11 \
    python3-pip \
    python3-dev \
    clang \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip and install Jupyter components
RUN python3 -m pip install --no-cache-dir --upgrade pip
RUN python3 -m pip install --no-cache-dir jupyterhub jupyterlab notebook

# Step 4: Install tinygrad from Source
# Cloning the repository ensures we get the latest version.
[cite_start]RUN git clone https://github.com/geohot/tinygrad.git /opt/tinygrad [cite: 7]
WORKDIR /opt/tinygrad
# Install in editable mode; this is good practice.
[cite_start]RUN python3 -m pip install --no-cache-dir -e . [cite: 8]

# Step 5: (Optional) Install PyTorch for Verification
# CHANGED: Updated the PyTorch wheel to cu130 to align with the CUDA version.
[cite_start]RUN python3 -m pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cu130 [cite: 9]

# Step 6: Create a Non-Root User for Jupyter
# Running containers as a non-root user is a security best practice.
# [cite_start]'jovyan' is the standard user for the Jupyter project. [cite: 10]
[cite_start]ARG NB_USER=jovyan [cite: 11]
ARG NB_UID=1000
ENV HOME="/home/${NB_USER}"
RUN adduser --disabled-password --gecos "Default user" --uid ${NB_UID} ${NB_USER}
WORKDIR ${HOME}
# Switch to the new user
[cite_start]USER ${NB_USER} [cite: 11]

# Step 7: Define the Entrypoint for JupyterHub
# This command starts the single-user notebook server that JupyterHub
