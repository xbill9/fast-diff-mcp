# Fast Diff MCP Server

[![CI](https://github.com/kweinmeister/fast-diff-mcp/actions/workflows/CI.yml/badge.svg)](https://github.com/kweinmeister/fast-diff-mcp/actions/workflows/CI.yml)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Built with Rust](https://img.shields.io/badge/built%20with-Rust-orange.svg)](https://www.rust-lang.org/)

A high-performance Model Context Protocol server that provides text diffing capabilities. This server enables LLMs to efficiently compare two blocks of text and receive the differences in the standard unified diff format.

The core logic is implemented in Rust for maximum speed, offering a significant performance improvement over standard Python libraries. The server exposes both the high-performance Rust implementation and Python's native `difflib` for comparison and flexibility.

### Available Tools

-   **`diff_tool_rust_similar`** - Compares two multiline strings using a high-performance Rust implementation (the `similar` crate's Myers diff algorithm). This is the recommended tool for performance.
    -   `original_text` (string, required): The original text content.
    -   `modified_text` (string, required): The modified text content.

-   **`diff_tool_python_difflib`** - Compares two multiline strings using the standard Python `difflib` library (Ratcliff/Obershelp algorithm). Useful for comparison or when `difflib`-specific behavior is required.
    -   `original_text` (string, required): The original text content.
    -   `modified_text` (string, required): The modified text content.

### Installation & Usage

This project is a Python package with a Rust extension, so it requires a compilation step. The recommended way to install and run it is from a local clone of the repository.

#### Prerequisites

-   Git
-   Python (>= 3.10)
-   [Rust Toolchain](https://www.rust-lang.org/tools/install)
-   [uv](https://github.com/astral-sh/uv) (for Python environment and package management)

#### Running from Source

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/<your-username>/fast-diff-mcp.git
    cd fast-diff-mcp
    ```

2.  **Install dependencies and compile the extension:**
    This command sets up a virtual environment and installs the package in editable mode.
    ```bash
    uv pip install -e .
    ```

3.  **Run the server:**
    From the root of the project directory, run:
    ```bash
    uv run server.py
    ```

### Configuration

To use this server with an MCP client, you must configure the client to run the server from your local clone of this repository. **The commands below assume you are running them from the root of the cloned `fast-diff-mcp` directory.**

#### Configure for Claude.app

Add the following to your Claude settings, replacing `/path/to/fast-diff-mcp` with the absolute path to where you cloned the repository.

<details>
<summary>Using uv (from source)</summary>

```json
{
  "mcpServers": {
    "diff": {
      "command": "uv",
      "args": ["run", "server.py"],
      "options": {
        "cwd": "/path/to/fast-diff-mcp"
      }
    }
  }
}
```
</details>


#### Configure for VS Code

Add the following JSON block to your User Settings (JSON) file or a `.vscode/mcp.json` file in your workspace. Remember to replace `/path/to/fast-diff-mcp` with the correct absolute path.

> Note that the `mcp` key is needed when using the `mcp.json` file.

<details>
<summary>Using uv (from source)</summary>

```json
{
  "mcp": {
    "servers": {
      "diff": {
        "command": "uv",
        "args": ["run", "server.py"],
        "options": {
          "cwd": "/path/to/fast-diff-mcp"
        }
      }
    }
  }
}
```
</details>


### Benchmarking

This repository includes a script to benchmark the performance of the Rust implementation against Python's `difflib`.

To run the benchmark, execute the following command from the project root:
```bash
uv run benchmark.py
```

### Deploying to Google Cloud Run with Docker

This project is optimized for deployment to serverless platforms like Google Cloud Run using its included multi-stage `Dockerfile`.

The most direct way to deploy is with the `gcloud` CLI, which will build the container and deploy it in a single step.

**Prerequisites:**
-   [Google Cloud SDK](https://cloud.google.com/sdk/docs/install) installed and authenticated (`gcloud auth login`).
-   A Google Cloud project with billing enabled and the Cloud Run API activated.

**To deploy, run the following commands:**
```bash
# 1. Set your project, region, and service name
export PROJECT_ID="your-google-cloud-project-id"
export REGION="us-central1"
export SERVICE_NAME="fast-diff-mcp"

# 2. Configure gcloud to use your project
gcloud config set project $PROJECT_ID

# 3. Deploy from source
# gcloud will build the image and deploy it to Cloud Run
gcloud run deploy $SERVICE_NAME \
  --allow-unauthenticated \
  --region=$REGION \
  --source .
```
After a few minutes, `gcloud` will provide a public URL for your service.

### Contributing

Pull requests are welcome! We encourage contributions to help improve this server. Whether you want to add new features, fix bugs, or improve documentation, your input is valuable.

### License

Fast Diff MCP Server is licensed under the Apache License 2.0. For more details, please see the `LICENSE` file in the project repository.