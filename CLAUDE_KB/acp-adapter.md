# ACP Adapter (`acp_adapter/`)

> Agent Communication Protocol server for IDE integration (VS Code, Zed, JetBrains).

## Files
| File | Purpose |
|------|---------|
| `entry.py` | CLI entry point, loads env, starts server |
| `server.py` | `HermesACPAgent` — full ACP lifecycle |
| `session.py` | `SessionManager` — thread-safe session management |
| `auth.py` | Provider detection for authentication |
| `permissions.py` | ACP ↔ Hermes permission bridge |
| `events.py` | Callback factories for agent events |
| `tools.py` | Tool name → ACP ToolKind mapping |

## HermesACPAgent (server.py)
Implements full ACP lifecycle:
- `initialize()` — agent capabilities
- `authenticate()` — provider detection
- `new_session()` — create conversation
- `load_session()` — restore from DB
- `resume_session()` — resume interrupted session
- `fork_session()` — branch conversation
- `list_sessions()` — enumerate sessions
- `prompt()` — send message and get response
- `cancel()` — cancel in-progress prompt
- `set_session_model()` — switch model
- `set_session_mode()` — switch mode
- `set_config_option()` — update config

## Session Persistence (session.py)
- `SessionManager` backed by `SessionDB` (`~/.hermes/state.db`)
- Thread-safe session creation and restoration
- Sessions survive process restarts
- `_make_agent()` factory creates AIAgent with `platform="acp"`

## Slash Commands (handled locally)
`/help`, `/model`, `/tools`, `/context`, `/reset`, `/compact`, `/version`

## Threading Model
- `ThreadPoolExecutor(max_workers=4)` for synchronous AIAgent instances
- `asyncio.run_coroutine_threadsafe()` bridges sync agent to async ACP events

## Entry Points
- `hermes acp` command
- `hermes-acp` command
- `python -m acp_adapter`
