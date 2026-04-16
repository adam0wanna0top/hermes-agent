# Hermes Agent — Claude Code Knowledge Base

> Progressive-disclosure index. Read this file first for orientation, then open specific module files when you need details.

## Project Summary
Hermes Agent v0.9.0 by Nous Research — a self-improving AI agent with 90+ tools, persistent memory, multi-platform messaging, and a skill system. Written in Python (>=3.11), runs as CLI, gateway daemon, or Docker container.

## Architecture at a Glance
```
run_agent.py (AIAgent) ──► agent/ (prompt, context, memory, LLM)
    │
    ├──► tools/ (40+ tools via ToolRegistry)
    ├──► skills/ (27 bundled + 14 optional skill categories)
    ├──► plugins/ (memory providers, context engines)
    ├──► cron/ (scheduled tasks)
    └──► gateway/ (20+ messaging platforms)
            └──► gateway/platforms/ (adapters)
```

## Entry Points
- `hermes` → `hermes_cli.main:main` (CLI)
- `hermes-agent` → `run_agent:main` (direct agent)
- `hermes-acp` → `acp_adapter.entry:main` (ACP server)

## Module Files

| File | Module | Description |
|------|--------|-------------|
| [overview.md](overview.md) | Architecture | High-level architecture, data flow, key design patterns |
| [agent-core.md](agent-core.md) | Core Loop | `AIAgent` class in `run_agent.py` — conversation loop, tool calling, streaming |
| [agent-module.md](agent-module.md) | Agent Internals | `agent/` package — prompt builder, context compressor, memory manager, LLM adapters |
| [llm-integration.md](llm-integration.md) | LLM Layer | Multi-provider LLM routing, credential pools, model metadata, error classification |
| [tools-system.md](tools-system.md) | Tools | ToolRegistry pattern, tool discovery, toolset distributions |
| [tools-catalog.md](tools-catalog.md) | Tools Catalog | Individual tool descriptions (browser, terminal, web, file, MCP, skills, etc.) |
| [gateway.md](gateway.md) | Gateway | Gateway daemon, session management, streaming, hooks, delivery routing |
| [gateway-platforms.md](gateway-platforms.md) | Platforms | 20+ platform adapters (Telegram, Discord, Slack, WhatsApp, etc.) |
| [skills-system.md](skills-system.md) | Skills | Skill loading, progressive disclosure, Skills Hub, security scanning |
| [plugins.md](plugins.md) | Plugins | Plugin discovery, memory providers, context engines |
| [memory-system.md](memory-system.md) | Memory | Built-in memory, frozen snapshot pattern, external memory providers |
| [cron-scheduler.md](cron-scheduler.md) | Cron | Job CRUD, scheduler tick, delivery targets |
| [acp-adapter.md](acp-adapter.md) | ACP | Agent Communication Protocol server for IDE integration |
| [cli.md](cli.md) | CLI | HermesCLI TUI, slash commands, skin engine, streaming display |
| [hermes-cli.md](hermes-cli.md) | CLI Package | `hermes_cli/` — subcommands, setup wizard, config, auth, models |
| [configuration.md](configuration.md) | Config | Config sources (env > yaml > json > defaults), directory layout |
| [state-storage.md](state-storage.md) | State | SessionDB (SQLite with FTS5), session/message persistence, migrations |
| [trajectories.md](trajectories.md) | Trajectories | ShareGPT format, compression for RL training data |
| [security.md](security.md) | Security | SSRF protection, secret redaction, skill scanning, approval system |
| [deployment.md](deployment.md) | Deployment | Docker, Nix, installation scripts |
| [testing.md](testing.md) | Testing | Test structure, pytest configuration, test patterns |
