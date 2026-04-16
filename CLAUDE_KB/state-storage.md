# State Storage (`hermes_state.py`)

> `SessionDB` — SQLite session store with FTS5 full-text search.

## Database
- SQLite with WAL mode (concurrent readers + single writer)
- Location: `~/.hermes/state.db`
- Schema version 6 with migration support

## Tables

### sessions
- `id` TEXT PRIMARY KEY
- `source` TEXT (platform)
- `model` TEXT
- `input_tokens`, `output_tokens` INTEGER
- `billing_route` TEXT
- `title` TEXT
- `created_at`, `ended_at` TIMESTAMP

### messages
- `id` INTEGER PRIMARY KEY
- `session_id` TEXT (FK → sessions)
- `role` TEXT (system/user/assistant/tool)
- `content` TEXT
- `tool_calls` JSON
- `reasoning` TEXT
- `created_at` TIMESTAMP

### FTS5 Index
Full-text search across message content for session search tool.

## Key Operations
- `create_session()` — new session with metadata
- `end_session()` / `reopen_session()` — lifecycle
- `append_message()` — add message to session
- `get_messages_as_conversation()` — retrieve full conversation
- `clear_messages()` — clear session messages
- `update_token_counts()` — track usage
- `set_session_title()` / `get_session_by_title()` — title management
- `search_sessions()` — FTS5 search across sessions

## Write Safety
- `_execute_write()` uses `BEGIN IMMEDIATE` with random jitter retry
- Avoids convoy effects from concurrent writers
- Periodic PASSIVE WAL checkpoints

## Schema Migration
- Version tracked in `schema_version` table
- Migrations add columns incrementally
- Backward compatible: new columns have defaults
