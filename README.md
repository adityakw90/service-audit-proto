# service-audit-proto

Protobuf definitions and gRPC contracts for the Audit Service. Standardizes event structures, audit logging interfaces, and cross-service activity tracking in an event-driven architecture.

## Directory Structure

- `proto/`: Contains the source `.proto` files defining the shared event model and service APIs.
- `gen/`: Contains the generated code from the proto definitions.
  - `go/`: Generated Go code.
  - `python/`: Generated Python code.

## Services Overview

This repository defines the following gRPC services:

### 1. IngestionService (`ingest.proto`)

Receives, normalizes, and persists high-frequency data from multiple sources.

- **RecordEvent**: Records one event.
- **RecordEvents**: Records a batch of events and returns an ordered result for each input.

### 2. QueryService (`query.proto`)

Provides read-only access to normalized data for analytics and reporting.

- **GetEvent**: Retrieves an event by its externally visible UID.
- **SearchEvents**: Searches by source, action, source event ID, actor, entity, and occurrence time.
- **ListSources**: Lists known event sources and optionally searches `name` with a case-insensitive substring match.
- **ListEntities**: Lists known entities and optionally searches name, ID, and type with a case-insensitive substring match.
- **ListActors**: Lists known actors and optionally searches name, ID, and type with a case-insensitive substring match.

Search and list operations use token-based pagination via `page_size`, `page_token`, and `next_page_token`.

### Event schema

The protobuf event model mirrors the externally visible columns in the
`service-audit-db` event table:

- `source`, `event_id`, and `action`
- `actor_type`, `actor_id`, and optional `actor_name`
- `entity_type`, `entity_id`, and optional `entity_name`
- JSON `metadata` and `occurred_at`
- Server-owned `uid` and `created_at` on query `Event` messages

Single-event ingestion fields are flattened into `RecordEventRequest`.
`RecordEventsRequest` contains repeated `RecordEventRequest` messages so the
same writable schema is used by both ingestion operations.

The internal database primary key is not exposed.

## Prerequisites

- Go 1.25.5 or newer in the Go 1.25 line (the module selects Go 1.25.7 as its toolchain)
- Python 3.12
- Poetry
- `protoc` (the Protocol Buffers compiler)

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

### 3. Install Poetry and generation dependencies

**For Linux/macOS:**

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

Then install Python dependencies:

```bash
make py-deps
```

## Usage

To generate Go code, first run `make go-deps`, then run this command from the repository root:

```bash
make go
```

This command will:

1. Create the `gen/go` directory if it doesn't exist.
2. Compile all `.proto` files in the `proto/` directory.
3. Output the generated Go code into `gen/go`, preserving the package structure defined by `go_package`.

### Python Code Generation

To generate Python code, first run `make py-deps`, then run:

```bash
make py
```

This command will:

1. Use the configured Python 3.12 Poetry environment.
2. Create the `gen/python` directory if it doesn't exist
3. Compile all `.proto` files using Python's gRPC tools
4. Run a post-processing script to organize common and service modules into package directories
5. Output the generated Python code into `gen/python/service_audit_proto/`

The generated Python package is named `service_audit_proto`. Install the current checkout with `poetry install`, or install a tagged revision from GitHub:

```bash
pip install "git+https://github.com/adityakw90/service-audit-proto.git@<version>"
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

Generated bindings are committed. Regenerate both targets after changing a source `.proto` file. `make clean` removes the entire `gen/` directory; run `make all` to restore it.
