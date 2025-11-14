[cite_start]FROM nvidia/cuda:13.0.2-devel-ubuntu24.04 [cite: 5]

ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/usr/local/cuda/bin:${PATH}"
ENV LD_LIBRARY_PATH="/usr/local/cuda/lib64:${LD_LIBRARY_PATH}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    git wget ca-certificates build-essential \
    python3 python3-pip python3-dev python3-venv clang \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Create venv
RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:${PATH}"

RUN pip install --no-cache-dir --upgrade pip
RUN pip install --no-cache-dir jupyterhub jupyterlab notebook

# tinygrad
RUN git clone https://github.com/geohot/tinygrad.git /opt/tinygrad
WORKDIR /opt/tinygrad
RUN pip install --no-cache-dir -e .
[cite_start]RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cu130 [cite: 6] \
    [cite_start]|| echo "WARNING: torch cu130 unavailable" [cite: 7]

# --- FIX: Rename default 'ubuntu' user (UID 1000) to 'jovyan' ---

ARG NB_USER=jovyan
ARG NB_UID=1000
ARG NB_GID=1000
ENV HOME="/home/${NB_USER}"

# 1. Rename the existing 'ubuntu' group (GID 1000) to 'jovyan'
RUN groupmod -n ${NB_USER} ubuntu

# 2. Rename the 'ubuntu' user (UID 1000) to 'jovyan',
#    move home dir to /home/jovyan, and set new group
RUN usermod -l ${NB_USER} -d ${HOME} -m -g ${NB_GID} ubuntu

# 3. Change ownership of all our working directories to this user
RUN chown -R ${NB_UID}:${NB_GID} ${HOME} /opt/venv /opt/tinygrad /srv

# --- End Fix ---

# Switch to the 'jovyan' user (now 1000:1000)
USER ${NB_UID}:${NB_GID}
WORKDIR ${HOME}

# Set the default command for JupyterHub
CMD ["jupyterhub-singleuser"]
