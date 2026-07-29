# service-audit-proto

Protobuf definitions and gRPC contracts for the Audit Service. Standardizes event structures, audit logging interfaces, and cross-service activity tracking in an event-driven architecture.

## Directory Structure

- `proto/`: Contains the source `.proto` files defining the service API and messages.
- `gen/`: Contains the generated code from the proto definitions.
  - `go/`: Generated Go code.
  - `python/`: Generated Python code.

## Services Overview

This repository defines the following gRPC services:

### 1. IngestService (`ingest.proto`)

Receives, normalizes, and persists high-frequency data from multiple sources.

- **RecordEvent**: Records an event.

### 2. QueryService (`query.proto`)

Provides read-only access to normalized data for analytics and reporting.

- **GetEvent**: Retrieves an event by ID.

## Installation

### 1. Install Protocol Buffers compiler

**For Ubuntu/Debian:**

```bash
sudo apt-get install protobuf-compiler
```

**For macOS:**

```bash
brew install protobuf
```

### 2. Install Go plugins

```bash
make go-deps
```

### 3. Install Poetry (for Python support)

**For Linux/macOS:**

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

Then install Python dependencies:

```bash
make py-deps
```

## Usage

To generate the Go code from the proto definitions, run the following command from the root of the repository:

```bash
make go
```

This command will:

1. Create the `gen/go` directory if it doesn't exist.
2. Compile all `.proto` files in the `proto/` directory.
3. Output the generated Go code into `gen/go`, preserving the package structure defined by `go_package`.

### Python Code Generation

To generate Python code from the proto definitions:

```bash
make py
```

This command will:

1. Ensure Python dependencies are installed (`make py-deps`)
2. Create the `gen/python` directory if it doesn't exist
3. Compile all `.proto` files using Python's gRPC tools
4. Run a post-processing script to organize the code into service-based directories
5. Output the generated Python code into `gen/python/service_audit_proto/`

The generated Python package is named `service_audit_proto` and can be installed from GitHub:

```bash
# Install latest from main branch
pip install git+https://github.com/adityakw90/service-audit-proto.git

# Install specific version tag
pip install git+https://github.com/adityakw90/service-audit-proto.git@v0.1.1
```

### Generate Both Go and Python

To generate code for both languages:

```bash
make all
```

## Key Commands

| Command        | Description                            |
| -------------- | -------------------------------------- |
| `make deps`    | Install all dependencies (Go + Python) |
| `make go-deps` | Install Go protobuf plugins only       |
| `make py-deps` | Install Python dependencies via Poetry |
| `make go`      | Generate Go code                       |
| `make py`      | Generate Python code                   |
| `make all`     | Generate both Go and Python code       |
| `make clean`   | Clean all generated code directories   |
