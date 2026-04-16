# Tools System

## Architecture

### ToolRegistry (`tools/registry.py`)
Central singleton where all tools self-register at import time.

```python
@dataclass
class ToolEntry:
    name: str
    toolset: str
    schema: dict          # OpenAI function schema
    handler: callable     # async or sync handler
    check_fn: callable    # availability check
    requires_env: list    # required env vars
    is_async: bool
    description: str
    emoji: str
    max_result_size_chars: int

class ToolRegistry:
    _instance: ToolRegistry  # Singleton
    _tools: dict[str, ToolEntry]
    _lock: threading.RLock   # Thread-safe

    def register(self, entry: ToolEntry) -> None
    def get(self, name: str) -> ToolEntry
    def all_tools(self) -> dict[str, ToolEntry]
    def tools_for_toolsets(self, toolsets: list[str]) -> dict[str, ToolEntry]
```

### Registration Pattern
Every tool file in `tools/` calls `registry.register()` at module scope:
```python
# tools/web_tools.py
registry.register(ToolEntry(
    name="web_search",
    toolset="web",
    schema={...},
    handler=_web_search,
    check_fn=lambda: bool(os.environ.get("EXA_API_KEY")),
    requires_env=["EXA_API_KEY"],
    ...
))
```

### Discovery Chain
1. `model_tools.py` imports all tool modules (triggering registration)
2. `_discover_tools()` calls `registry.tools_for_toolsets(enabled_toolsets)`
3. Tool schemas converted to OpenAI function-calling format
4. MCP tools merged in via `mcp_tool.discover_mcp_tools()`

### Toolsets (`toolsets.py`)
Tools are grouped into named toolsets:
- `_HERMES_CORE_TOOLS` — always enabled (memory, skill_view, etc.)
- `web`, `browser`, `terminal`, `file`, `code_execution`, `mcp`, `skills`, `vision`, `tts`, `voice`, `cron`, `delegate`, `search`, `messaging`, `image_generation`, `rl_training`, `browser_camofox`

Platforms enable different toolsets:
- CLI: most toolsets
- Gateway: subset based on config
- ACP: minimal set
- Cron: minimal set

### Tool Handler Interface
```python
async def handler(**kwargs) -> str:
    """Returns string result to be appended as tool message."""
```

Some handlers are sync (wrapped automatically). All return a string result.

### handle_function_call() (`model_tools.py`)
1. Parse `function.name` and `function.arguments` from LLM response
2. Look up tool in registry
3. Call handler with parsed arguments
4. Truncate result to `max_result_size_chars`
5. Return formatted result string
