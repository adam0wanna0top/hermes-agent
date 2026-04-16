# Gateway System (`gateway/`)

> Single process connecting to 20+ messaging platforms.

## Core Files

| File | Lines | Purpose |
|------|-------|---------|
| `run.py` | 9,289 | Gateway daemon — message dispatch, slash commands |
| `config.py` | 1,161 | `GatewayConfig`, `PlatformConfig`, platform enum |
| `session.py` | 1,087 | `SessionStore`, `SessionContext`, session management |
| `delivery.py` | ~200 | `DeliveryRouter`, `DeliveryTarget` |
| `hooks.py` | ~300 | `HookRegistry` — event hooks |
| `stream_consumer.py` | ~300 | Progressive streaming via message editing |
| `pairing.py` | ~300 | User authorization via pairing codes |
| `mirror.py` | ~200 | Cross-platform message mirroring |
| `status.py` | ~100 | PID-file process detection |
| `channel_directory.py` | ~200 | Channel name resolution |
| `display_config.py` | ~100 | Per-platform display settings |

## Session Management

### SessionStore
- `get_or_create_session(source)` — creates or retrieves with auto-reset
- Session key: deterministic from platform + chat_type + chat_id + thread_id + user_id
- Dual storage: SQLite (via SessionDB) + JSONL (legacy fallback)

### SessionResetPolicy
- `daily` — reset at midnight (configurable timezone)
- `idle` — reset after N minutes of inactivity
- `both` — whichever comes first
- `none` — never auto-reset

### SessionSource
Describes message origin: platform, chat_id, chat_type (dm/group/channel/thread), user_id, thread_id

### SessionContext
Full context for system prompt injection: source, connected platforms, home channels, session metadata

## Streaming (`stream_consumer.py`)
- Bridges sync agent callbacks to async platform delivery
- Edit transport: send initial message → progressively edit with new content
- Rate-limited with configurable interval and buffer threshold
- Flood-control detection with auto-disable after 3 failures
- Strips thinking/reasoning tags from streamed content

## Event Hooks (`hooks.py`)
Events:
- `gateway:startup` — gateway process started
- `session:start` — new session created
- `session:end` — session ended
- `session:reset` — session reset
- `agent:start` — agent processing started
- `agent:step` — agent completed a step
- `agent:end` — agent processing completed
- `command:*` — slash command executed

Hooks discovered from `~/.hermes/hooks/` directories (HOOK.yaml + handler.py).
Built-in: `boot-md` (runs BOOT.md on startup).

## Delivery Routing (`delivery.py`)
Targets: `origin` (back to source), `local` (files), `telegram:123456` (specific chat)
Used by cron jobs and send_message tool.

## Message Flow
1. Platform adapter receives message → creates `SessionSource`
2. `SessionStore.get_or_create_session()` evaluates reset policy
3. `build_session_context()` injects context into system prompt
4. `AIAgent` processes message with LLM
5. Response delivered via platform adapter (with optional streaming)
6. `SessionStore.update_session()` records metadata

## Concurrency
- `session_context.py` uses `contextvars.ContextVar` for per-task isolation
- Replaces `os.environ`-based session state (which caused race conditions)
- Variables: platform, chat_id, chat_name, thread_id, user_id, user_name, session_key
