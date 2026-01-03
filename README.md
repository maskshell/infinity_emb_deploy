# Infinity Embedding Deployment Environment

A pre-configured deployment environment for [infinity_emb](https://github.com/michaelfeil/infinity) with all necessary dependencies including PyTorch, ONNX Runtime, and FastAPI server components. This package is especially useful for non-Docker deployment scenarios.

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

## Process Compose Management

This project includes a `process-compose.yaml` configuration file for managing the infinity_emb service using [Process Compose](https://github.com/F1bonacc1/process-compose), a process orchestrator for non-containerized applications.

### Start Service (Background Mode)

```bash
process-compose up -f process-compose.yaml -D -t=false -p 18081
```

**Parameters:**

- `up` - Starts all processes defined in the configuration
- `-f process-compose.yaml` - Specifies the configuration file to use
- `-D, --detached` - Runs process-compose in detached (background) mode
- `-t=false` - Disables TUI (Terminal User Interface), required for background operation
- `-p 18081` - Sets the HTTP API server port (default: 8080). Use different ports when running multiple instances

### Attach to Running Instance

```bash
process-compose attach -a localhost -p 18081
```

**Parameters:**

- `attach` - Connects to a running process-compose instance in TUI mode
- `-a localhost` - Address of the target process-compose server (default: localhost)
- `-p 18081` - Port number of the target instance (must match the port used when starting)

This command opens an interactive TUI where you can monitor process status, view logs, and manage processes.

### List Processes

```bash
process-compose list --address localhost -p 18081
```

**Parameters:**

- `list` - Lists all available processes managed by the instance
- `--address localhost` - Address of the target process-compose server
- `-p 18081` - Port number of the target instance

This command shows the current status of all processes (running, stopped, etc.) in JSON or wide format.

### Stop Service

```bash
process-compose down -a localhost -p 18081
```

**Parameters:**

- `down` - Stops all running processes and terminates the process-compose instance
- `-a localhost` - Address of the target process-compose server
- `-p 18081` - Port number of the target instance

This gracefully shuts down all processes and the process-compose orchestrator itself.

### Running Multiple Instances

To run multiple instances simultaneously, use different ports for each:

```bash
# Instance 1
process-compose -f process-compose.yaml up -D -t=false -p 18081

# Instance 2
process-compose -f process-compose-2.yaml up -D -t=false -p 18082
```

Each instance requires:

- A unique port number (`-p`)
- Optionally, a different configuration file (`-f`)
- Different unix socket paths (automatically handled based on PID)

## Notes

- Set `TOKENIZERS_PARALLELISM=false` to avoid tokenizer warnings in some environments
- Process Compose logs are stored in `./logs/infinity_emb.log` as configured
- The service automatically restarts on failure with exponential backoff
