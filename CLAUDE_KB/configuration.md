# Configuration System

## Config Priority (highest to lowest)
1. Environment variables
2. `~/.hermes/config.yaml`
3. `~/.hermes/gateway.json` (legacy)
4. Built-in defaults

## Directory Layout
```
~/.hermes/
├── config.yaml          # Main user config
├── .env                 # Environment variables
├── state.db             # SQLite (sessions + messages)
├── agent.log            # Agent log (INFO+)
├── errors.log           # Error log (WARNING+)
├── gateway.log          # Gateway log (gateway mode only)
├── auth.json            # Auth tokens (Nous Portal, OAuth)
├── skills/              # Installed skills
├── memories/            # Memory files
│   ├── MEMORY.md        # Agent memory
│   └── USER.md          # User profile
├── cron/                # Cron jobs
│   └── jobs.json        # Job definitions
├── hooks/               # Event hooks
├── profiles/            # Named profiles
├── mcp/                 # MCP server configs
├── sessions/            # Session transcripts (legacy JSONL)
└── state.db             # Session database (SQLite with FTS5)
```

## Key Config Modules
| Module | File | Purpose |
|--------|------|---------|
| Gateway config | `gateway/config.py` | Platform settings, session policies, streaming |
| CLI config | `hermes_cli/config.py` | Config.yaml loading/saving |
| Constants | `hermes_constants.py` | Path resolution, env detection |
| Env template | `.env.example` | 388-line comprehensive template |

## Environment Variables (`.env.example`)
Key variables:
- `OPENROUTER_API_KEY` — OpenRouter API key
- `ANTHROPIC_API_KEY` — Anthropic API key
- `GOOGLE_AI_API_KEY` — Google Gemini API key
- `HERMES_HOME` — Override default ~/.hermes
- `HERMES_TIMEZONE` — Timezone for scheduling
- Platform-specific: `TELEGRAM_BOT_TOKEN`, `DISCORD_BOT_TOKEN`, etc.
- Tool-specific: `EXA_API_KEY`, `FIRECRAWL_API_KEY`, etc.

## Config Loading
- `hermes_constants.get_hermes_home()` resolves `HERMES_HOME` or `~/.hermes`
- `hermes_cli/config.py` loads config.yaml with PyYAML
- `gateway/config.py._apply_env_overrides()` maps ~50 env vars to config
- Lazy loading: config loaded on first access, cached for process lifetime
