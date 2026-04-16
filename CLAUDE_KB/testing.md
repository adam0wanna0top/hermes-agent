# Testing

## Test Structure (`tests/`)
17 subdirectories, ~3000 tests total:

| Directory | Tests |
|-----------|-------|
| `acp/` | ACP adapter |
| `agent/` | Agent logic |
| `cli/` | CLI behavior |
| `cron/` | Cron scheduler |
| `e2e/` | End-to-end flows |
| `environments/` | Execution environments |
| `fakes/` | Test fakes/mocks |
| `gateway/` | Gateway platforms |
| `hermes_cli/` | CLI package |
| `integration/` | Integration tests (marked) |
| `plugins/` | Plugin system |
| `run_agent/` | Agent runner |
| `skills/` | Skills system |
| `tools/` | Tool implementations (largest) |

## Key Root-Level Test Files
| File | Size | Tests |
|------|------|-------|
| `test_hermes_state.py` | 58KB | SQLite state store |
| `test_mcp_serve.py` | 44KB | MCP server |
| `test_trajectory_compressor.py` | 16KB | Trajectory compression |
| `test_timezone.py` | 16KB | Timezone handling |
| `test_batch_runner_checkpoint.py` | 5KB | Batch runner |

## Configuration (pyproject.toml)
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-m 'not integration' -n auto"
```

- Integration tests excluded by default (require real API keys)
- Parallel execution via pytest-xdist (`-n auto`)
- Shared fixtures in `conftest.py`

## Test Patterns
- Fixtures for mock LLM responses
- Fake tool registry for unit tests
- SQLite in-memory databases for state tests
- Mock platform adapters for gateway tests
