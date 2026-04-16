# Architecture Overview

## System Layers

### Layer 1: Agent Core (`run_agent.py` + `agent/`)
The `AIAgent` class runs a synchronous tool-calling loop:
1. Send messages to LLM
2. Parse response for `tool_calls`
3. Execute each tool via `handle_function_call()`
4. Append tool results to messages
5. Repeat until no more `tool_calls`

Key files: `run_agent.py` (10,871 lines), `model_tools.py` (tool orchestration), `toolsets.py` (toolset definitions).

### Layer 2: Interactive CLI (`cli.py` + `hermes_cli/`)
Rich TUI built on `prompt_toolkit` with:
- Multiline editing, syntax highlighting
- Slash commands (/help, /model, /tools, /reset, /compact, etc.)
- Streaming tool output with spinner
- Skin/theme engine
- Shell completion (bash/zsh/fish)

### Layer 3: Multi-Platform Gateway (`gateway/`)
Single process connecting to 20+ messaging platforms:
- Receives messages via platform adapters
- Creates/loads sessions with reset policies (daily/idle/both/none)
- Builds session context for system prompt injection
- Runs AIAgent per message
- Delivers responses with progressive streaming

## Data Flow
```
User Message → Platform Adapter → SessionStore → AIAgent → LLM API
                                                           ↓
                                                    Tool Calls → ToolRegistry → Tool Execution
                                                           ↓
                                                    Tool Results → AIAgent → LLM API (loop)
                                                           ↓
Final Response → Platform Adapter → User
```

## Key Design Patterns

| Pattern | Where | Purpose |
|---------|-------|---------|
| Registry | `tools/registry.py` | Tools self-register at import time via `registry.register()` |
| Adapter | `gateway/platforms/` | `BasePlatformAdapter` ABC with 20+ implementations |
| Strategy | `agent/context_engine.py` | Pluggable context engines (default: `ContextCompressor`) |
| Provider ABC | `agent/memory_provider.py` | Pluggable memory providers (Honcho, mem0, etc.) |
| Observer | `gateway/hooks.py` | Event hooks (gateway:startup, session:start, agent:step, etc.) |
| Frozen Snapshot | `tools/memory_tool.py` | Memory snapshot frozen at load for prefix cache stability |
| Chain of Responsibility | `agent/auxiliary_client.py` | Provider resolution chain with automatic failover |
| Atomic Write | `utils.py` | temp-file + fsync + os.replace for crash safety |

## Three Largest Files (Backbone)

| File | Lines | Role |
|------|-------|------|
| `run_agent.py` | 10,871 | `AIAgent` — core conversation loop |
| `cli.py` | 10,017 | `HermesCLI` — interactive TUI |
| `gateway/run.py` | 9,289 | Gateway daemon — message dispatch |

## Configuration Priority
1. Environment variables (highest)
2. `~/.hermes/config.yaml`
3. `~/.hermes/gateway.json` (legacy)
4. Built-in defaults

## Directory Layout
```
~/.hermes/
├── config.yaml          # Main config
├── .env                 # Environment vars
├── state.db             # SQLite (sessions + messages)
├── agent.log            # Agent log
├── errors.log           # Error log
├── skills/              # Installed skills
├── memories/            # Memory files (MEMORY.md, USER.md)
├── cron/                # Cron job definitions
├── hooks/               # Event hooks
├── auth.json            # Auth tokens
└── profiles/            # Named profiles
```
