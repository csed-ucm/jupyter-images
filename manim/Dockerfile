# Base image requested
FROM docker.io/manimcommunity/manim:latest

# Switch to root to install packages and modify users
USER root

# Install JupyterHub, JupyterLab, and Notebook
# We install into the system Python environment to preserve access to the pre-installed manim library
RUN pip install --no-cache-dir \
    jupyterhub \
    jupyterlab \
    notebook

# --- FIX: Rename default 'manimuser' user (UID 1000) to 'jovyan' ---
# This ensures compatibility with standard JupyterHub deployments (like 'tinygrad/Dockerfile')

ARG NB_USER=jovyan
ARG NB_UID=1000
ARG NB_GID=1000
ENV HOME="/home/${NB_USER}"

# 1. Rename the existing 'manimuser' group (GID 1000) to 'jovyan'
RUN groupmod -n ${NB_USER} manimuser

# 2. Rename the 'manimuser' user (UID 1000) to 'jovyan',
#    move home dir to /home/jovyan (renaming /manim), and set the new group
#    (The -m flag moves the content of the old home dir /manim to the new location)
RUN usermod -l ${NB_USER} -d ${HOME} -m -g ${NB_GID} manimuser

# 3. Change ownership of the new home directory to ensure permissions are correct
RUN chown -R ${NB_UID}:${NB_GID} ${HOME}

# --- End Fix ---

# Switch to the 'jovyan' user (UID 1000)
USER ${NB_UID}:${NB_GID}
WORKDIR ${HOME}

# Set the default command to start the JupyterHub single-user server
CMD ["jupyterhub-singleuser"]
