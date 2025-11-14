FROM nvidia/cuda:13.0.2-devel-ubuntu24.04

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

RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cu130 \
    || echo "WARNING: torch cu130 unavailable"

# --- IMPORTANT PART ---
# Ensure directory permissions match JupyterHub users (UID=1000)
RUN mkdir -p /home/ubuntu && \
    chown -R 1000:1000 /home/ubuntu /opt/venv /opt/tinygrad /srv

USER 1000:1000
WORKDIR /home/ubuntu
