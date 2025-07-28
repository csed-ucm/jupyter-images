FROM quay.io/jupyter/datascience-notebook:latest

# Switch to root to install additional packages
RUN pip install --no-cache-dir \
	langchain \
	langchain-ollama \
	langchain-qdrant \
	langchain-community \
	langchain-text-splitters \
	qdrant-client \
	devtools \
	dotenv \
	pydantic \
	mlflow \
	ollama
