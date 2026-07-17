# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Colima (Containers on Lima) is a Go CLI application that provides container runtimes on macOS and Linux with minimal setup. It leverages Lima (Linux Machines) for virtualization and supports multiple container runtimes including Docker, Containerd, and Incus.

## Development Commands

### Building
```bash
# Build the binary
make build

# Build for specific OS/architecture
GOOS=darwin GOARCH=arm64 make build

# Clean build artifacts
make clean
```

### Testing
```bash
# Run unit tests
make test
# or
go test -v ./...

# Run integration tests (requires built binary)
make integration

# Run specific test
go test -v ./path/to/package -run TestName
```

### Code Quality
```bash
# Format code
make fmt

# Lint code (requires golangci-lint)
make lint

# Install dependencies
make gopath
# or
go get -v ./cmd/colima
```

### Installation
```bash
# Install locally
make install
```

## Architecture

### Key Directories
- `cmd/` - CLI command implementations and entry points
- `app/` - Core application logic and orchestration
- `cli/` - Command-line interface processing
- `config/` - Configuration management with template support
- `environment/` - Runtime environment handling
  - `environment/vm/lima/` - Lima VM management (primary virtualization)
  - `environment/container/` - Container runtime support
- `daemon/` - Background process and service management
- `store/` - Data persistence and state management
- `util/` - Shared utilities and helpers
- `embedded/` - Embedded resources and assets
- `integration/` - Integration tests

### Core Architecture Patterns
- **CLI Framework**: Uses Cobra for command structure
- **Profile-based**: Supports multiple named instances/profiles
- **Runtime Agnostic**: Abstraction layer supporting Docker, Containerd, Incus
- **VM-based**: Leverages Lima for Linux VM management on macOS
- **Configuration-driven**: YAML-based configuration with templates
- **Cross-platform**: Intel and Apple Silicon support

### Key Entry Points
- `cmd/colima/main.go` - Application entry point
- `cmd/root/root.go` - Root command and global flags
- `app/app.go` - Core application logic
- `environment/vm/lima/lima.go` - VM management implementation
- `config/config.go` - Configuration handling

### Container Runtime Support
The application abstracts container runtime operations through interfaces, with implementations for:
- Docker (default runtime)
- Containerd (with nerdctl integration)
- Incus (containers and VMs)
- None (headless VM mode)

### VM Management
- Uses Lima as the primary virtualization backend
- Supports customizable VM parameters (CPU, memory, disk)
- Automatic port forwarding and volume mounting
- Cross-architecture emulation (Rosetta 2 on Apple Silicon)

## Testing Strategy

### Unit Tests
- Located alongside source files with `_test.go` suffix
- Use standard Go testing framework
- Cover individual package functionality

### Integration Tests
- Located in `integration/` directory
- Test complete workflows across multiple runtimes
- Script-based testing in `scripts/integration.sh`
- Test both Intel and ARM architectures

### Test Execution
Integration tests require:
1. Built binary (`make build`)
2. Docker and kubectl installed
3. Various container runtimes available

## Configuration System

### Template System
- Default templates in `~/.colima/_templates/`
- Command: `colima template` for editing
- YAML-based configuration format
- Runtime-specific settings supported

### Profile Management
- Multiple instances via `--profile` flag
- Environment variable support (`COLIMA_PROFILE`)
- Per-profile configuration and state

## Development Patterns

### Error Handling
- Structured logging with logrus
- CLI-friendly error messages
- Graceful degradation for optional features

### Cross-platform Support
- Build-time OS/architecture detection
- Runtime feature detection
- Apple Silicon specific optimizations (VZ, Rosetta 2)

### State Management
- Persistent storage in `store/` package
- Profile-based state isolation
- VM lifecycle management

## Important Implementation Details

### Lima Integration
- Lima YAML configuration generation
- SSH key management for VM access
- Port forwarding and volume mount automation
- Network configuration for container access

### Container Runtime Abstraction
- Interface-based design for runtime pluggability
- Runtime-specific client handling
- Kubernetes integration support

### Daemon Management
- Background process handling for vmnet
- Process supervision and lifecycle management
- IPC mechanisms for daemon communication