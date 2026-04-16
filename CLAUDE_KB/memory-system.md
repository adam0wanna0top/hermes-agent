# Memory System

## Built-in Memory (`tools/memory_tool.py`, 585 lines)

### Architecture
- `MemoryStore` class with two parallel states: live entries and frozen `_system_prompt_snapshot`
- Two targets: `memory` (agent notes, 2200 char limit) and `user` (user profile, 1375 char limit)
- File-backed: `MEMORY.md` and `USER.md` in `~/.hermes/memories/`
- Atomic writes via temp file + `os.replace()`
- File locking via `fcntl` (Unix) or `msvcrt` (Windows)

### Frozen Snapshot Pattern
- `format_for_system_prompt()` returns the **frozen** snapshot (NOT live state)
- Snapshot frozen at load time for system prompt injection
- Preserves prefix cache stability across conversation turns
- Live state updated by tool calls but snapshot remains stable

### Content Scanning
- Scans for injection patterns (prompt injection attempts)
- Scans for exfiltration patterns (attempts to read other users' memory)
- Rejects suspicious content before writing

## MemoryManager (`agent/memory_manager.py`)
- Orchestrates builtin + at most one external `MemoryProvider`
- Routes tool calls via `_tool_to_provider` dict
- `prefetch_all()` — prefetch from all providers
- `sync_all()` — sync turn data to all providers
- `build_system_prompt()` — combine prompt blocks from all providers
- Failures in one provider never block the other

## External Memory Providers
See [plugins.md](plugins.md) for details on each provider:
- Holographic — SQLite + HRR vectors
- Honcho — Cloud API, three recall modes
- Mem0 — Cloud API, circuit breaker
- Supermemory — Semantic long-term memory
- Byterover, OpenViking, RetainDB, Hindsight

## Memory in Conversation Flow
1. Agent starts → MemoryManager.prefetch_all() with initial query
2. Memory blocks injected into system prompt via build_system_prompt()
3. Agent can call memory tools (read/write) during conversation
4. Each turn → MemoryManager.sync_all() with conversation data
5. Session end → MemoryProvider.on_session_end()
6. Before compression → MemoryProvider.on_pre_compress()
