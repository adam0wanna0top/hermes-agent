# Deployment

## Docker (`Dockerfile`)
Multi-stage build:
- Base: `debian:13.4`
- Python via `uv` package manager
- Playwright with Chromium for browser tool
- Node.js + npm for WhatsApp bridge
- Non-root `hermes` user
- Persistent volume: `/opt/data`

### Entrypoint (`docker/entrypoint.sh`)
- Bootstraps config into mounted volume
- Syncs bundled skills
- Creates directory structure
- Drops root via `gosu`

## Installation Scripts
| Script | Platform | Purpose |
|--------|----------|---------|
| `scripts/install.sh` (54KB) | Linux/macOS | Main installer |
| `scripts/install.ps1` (36KB) | Windows | PowerShell installer |
| `scripts/install.cmd` | Windows | CMD stub |
| `setup-hermes.sh` | Any | Developer setup |

## Nix (`flake.nix`)
- Full flake with packages, NixOS modules, checks
- Dev shell for development
- Reproducible builds

## Homebrew (`packaging/`)
- Homebrew formula for macOS installation

## Running Modes
1. **CLI**: `hermes` or `hermes-agent` — interactive terminal
2. **Gateway**: `hermes gateway start` — multi-platform daemon
3. **Docker**: `docker run` — containerized deployment
4. **ACP**: `hermes acp` — IDE integration server

## Environment Backends (for terminal tool)
| Backend | Config | Purpose |
|---------|--------|---------|
| local | default | Direct execution |
| docker | `TERMINAL_BACKEND=docker` | Docker container |
| ssh | `TERMINAL_BACKEND=ssh` | Remote via SSH |
| modal | `TERMINAL_BACKEND=modal` | Modal serverless |
| daytona | `TERMINAL_BACKEND=daytona` | Daytona sandbox |
| singularity | `TERMINAL_BACKEND=singularity` | Singularity container |
