# Infinity Embedding Deployment Environment

A pre-configured deployment environment for [infinity_emb](https://github.com/michaelfein/infinity) with all necessary dependencies including PyTorch, ONNX Runtime, and FastAPI server components. This package is especially useful for non-Docker deployment scenarios.

## Background

In macOS environments, directly installing `pip install infinity_emb[server,torch]` often fails due to complex dependency conflicts. This project extracts the correct dependency versions from the development environment and explicitly lists them as an alternative to the `[server,torch]` installation method, preventing incorrect dependency versions from being installed.

## Requirements

- Python 3.12
- [uv](https://github.com/astral-sh/uv) package manager

## Installation

```bash
git clone https://github.com/maskshell/infinity_emb_deploy.git infinity && cd infinity
uv venv --python 3.12
uv sync
```

## Usage

### Check Installation

```bash
uv run infinity_emb v2 --help
```

### Example: Run Server with Embedding and Reranker Models

```bash
TOKENIZERS_PARALLELISM=false \
uv run infinity_emb v2 \
   --model-id BAAI/bge-m3 \
   --model-id BAAI/bge-reranker-v2-m3 \
   --device mps \
   --port 7997
```

### Device Options

- `mps` - Apple Silicon GPU (Metal Performance Shaders)
- `cpu` - CPU inference
- etc.

## Notes

- Set `TOKENIZERS_PARALLELISM=false` to avoid tokenizer warnings in some environments
