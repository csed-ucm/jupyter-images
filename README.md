# CSED Jupyter Images

Public Docker images providing pre-configured Jupyter environments for Computer Science and Engineering coursework and research. These images are specifically optimized for deployment on Kubernetes-based JupyterHub clusters, standardizing the default user environment to ensure seamless integration.

## Available Environments

We maintain a matrix of Jupyter environments to support various computational needs, from lightweight scripting to GPU-accelerated deep learning:

* **Minimal environment**: Only Python, no additional packages.
* **Datascience environment**: Python, R, and Julia with additional packages.
* **Pytorch environment**: The PyTorch Stacks with CUDA support.
* **Tensorflow environment**: The TensorFlow Stacks with CUDA support.
* **Test Forge**: Built on the standard Jupyter Datascience notebook, this image includes Qdrant, Langchain, and Ollama for LLM and vector database experimentation.
* **Tinygrad**: A CUDA 13.0.2 based environment equipped with the Tinygrad framework and PyTorch.
* **Manim**: Built on the Manim Community base image, providing a Python library for creating mathematical animations.

## JupyterHub Compatibility

To ensure out-of-the-box compatibility with standard JupyterHub deployments, our custom Dockerfiles (such as Manim and Tinygrad) are engineered to remap their default users. The images systematically rename the base users to a standard `jovyan` profile with UID/GID 1000 and migrate the home directories accordingly. 

This standardization guarantees correct permissions when mounting persistent user volumes in a cluster environment. Additionally, these environments are explicitly configured to launch the `jupyterhub-singleuser` server by default.

## Usage & CI/CD

Images are automatically built and published to the GitHub Container Registry (GHCR) upon updates to the `main` branch. 

You can pull a specific environment using the directory name as the tag:

```bash
docker pull ghcr.io/csed-ucm/jupyter-images:<environment-name>
