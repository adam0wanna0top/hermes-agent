# Hermes CLI Package (`hermes_cli/`)

> 60+ modules totaling over 2MB of Python code. CLI subcommands and management.

## Key Files

| File | Lines | Purpose |
|------|-------|---------|
| `main.py` | ~5,900 | CLI entry point, subcommand routing |
| `commands.py` | ~1,070 | CLI command definitions |
| `config.py` | ~3,200 | Config management (config.yaml) |
| `setup.py` | ~3,000 | Interactive setup wizard |
| `gateway.py` | ~3,000 | Gateway management commands |
| `models.py` | ~1,700 | Model listing and selection |
| `auth.py` | ~3,000 | Authentication (API keys, OAuth) |
| `runtime_provider.py` | ~880 | Provider resolution (OpenRouter, Google, GLM, etc.) |
| `tools_config.py` | ~1,670 | Tool configuration |
| `skills_hub.py` | ~1,100 | Skills marketplace |
| `skin_engine.py` | ~930 | Theming system |
| `web_server.py` | ~1,800 | Built-in web server |
| `doctor.py` | ~1,100 | Diagnostic/troubleshooting |
| `profiles.py` | ~880 | Profile management |
| `plugins.py` | ~640 | Plugin management |
| `model_switch.py` | ~950 | Model switching logic |

## Subcommands (via `hermes` command)
- `hermes` — start interactive CLI
- `hermes setup` — interactive setup wizard
- `hermes gateway` — manage gateway (start/stop/status)
- `hermes models` — list/select models
- `hermes auth` — manage authentication
- `hermes doctor` — run diagnostics
- `hermes config` — view/edit config
- `hermes skills` — manage skills
- `hermes plugins` — manage plugins
- `hermes acp` — start ACP server

## Runtime Provider Resolution (`runtime_provider.py`)
Determines which LLM provider to use:
1. Check for OpenRouter API key
2. Check for Nous Portal auth
3. Check for Anthropic API key
4. Check for Google AI API key
5. Check for GLM/z.ai credentials
6. Check for Kimi/Moonshot credentials
7. Check for custom endpoint in config
8. Fallback to interactive setup
