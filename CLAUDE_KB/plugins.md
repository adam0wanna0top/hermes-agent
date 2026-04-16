# Plugins (`plugins/`)

## Structure
```
plugins/
├── memory/                  # Memory provider plugins
│   ├── holographic/         # Holographic (HRR) memory
│   ├── honcho/              # Honcho AI-native memory
│   ├── mem0/                # Mem0 platform memory
│   ├── supermemory/         # Supermemory provider
│   ├── byterover/           # Byterover memory
│   ├── openviking/          # OpenViking memory
│   ├── retaindb/            # RetainDB memory
│   └── hindsight/           # Hindsight memory
└── context_engine/          # Context engine plugins
```

## Plugin Discovery
- Each plugin directory has a `register(ctx)` entry point in `__init__.py`
- Discovery system scans subdirectories for providers
- Uses `_ProviderCollector` / `_EngineCollector` fake context objects
- Only one memory provider and one context engine active at a time

## Memory Providers

| Provider | Storage | Tools | Key Feature |
|----------|---------|-------|-------------|
| Built-in | File-backed (MEMORY.md, USER.md) | `memory`, `user` | Frozen snapshot for prefix cache stability |
| Holographic | SQLite + HRR vectors | `fact_store`, `fact_feedback` | Auto fact extraction at session end |
| Honcho | Cloud (Honcho API) | `honcho_profile`, `honcho_search`, `honcho_context`, `honcho_conclude` | Three recall modes, cost-awareness |
| Mem0 | Cloud (Mem0 API) | `mem0_profile`, `mem0_search`, `mem0_conclude` | Server-side LLM extraction, circuit breaker |
| Supermemory | Cloud (Supermemory API) | `supermemory_store`, `supermemory_search`, `supermemory_forget`, `supermemory_profile` | Semantic long-term memory |

## MemoryProvider ABC (`agent/memory_provider.py`)
```python
class MemoryProvider(ABC):
    async def initialize(self, agent) -> None
    def system_prompt_block(self) -> str
    async def prefetch(self, query: str) -> None
    async def sync_turn(self, messages: list) -> None
    def get_tool_schemas(self) -> list[dict]
    async def handle_tool_call(self, name: str, args: dict) -> str
    async def shutdown(self) -> None
    # Optional hooks:
    async def on_turn_start(self) -> None
    async def on_session_end(self) -> None
    async def on_pre_compress(self) -> None
    async def on_memory_write(self, content: str) -> None
    async def on_delegation(self, task: str) -> None
```

## Context Engine Plugins
- Abstract base: `agent/context_engine.py`
- Default implementation: `agent/context_compressor.py`
- Pluggable via `plugins/context_engine/`
