# Agent Internals (`agent/`)

> 27 Python files providing agent support utilities. Extracted from run_agent.py for maintainability.

## Key Files

### prompt_builder.py (46 KB)
Assembles the system prompt from multiple sources:
1. Identity text (from config or default)
2. Memory guidance (from MemoryManager)
3. Platform hints (CLI, Telegram, Discord, etc.)
4. Skills index (active skill summaries)
5. Context files (AGENTS.md, .hermes.md, .cursorrules)
6. Memory provider blocks
7. Session context (platform, user, connected channels)

Security: scans context files for injection patterns before loading.

### context_compressor.py (37 KB)
`ContextCompressor` extends `ContextEngine` ABC:
- Algorithm: prune old tool results → protect head (system + first exchange) → protect tail (~20K tokens) → summarize middle with LLM
- Uses `auxiliary_client.call_llm()` for summarization (cheap model)
- Scales summary budget proportionally (20% ratio, 12K ceiling)
- Configurable: `threshold_percent` (0.75), `protect_first_n` (3), `protect_last_n` (6)

### auxiliary_client.py (110 KB)
Unified interface for auxiliary LLM tasks:
- `call_llm()` — single function for compression, title generation, vision, web extraction, session search
- Provider resolution chain: OpenRouter → Nous Portal → Custom → Codex OAuth → Anthropic → Direct providers
- Automatic 402/credit exhaustion failover

### anthropic_adapter.py (57 KB)
Translates between OpenAI and Anthropic Messages API formats:
- Auth: API keys, OAuth tokens, Claude Code credentials
- Thinking budget: xhigh=32K, high=16K, medium=8K, low=4K
- Model output limits: Claude Opus/Sonnet 4.6 = 128K/64K

### credential_pool.py (58 KB)
Multi-credential management for provider failover:
- Strategies: fill_first, round_robin, random, least_used
- Exhausted credentials cool down after 1 hour
- Supports OAuth tokens and API keys

### model_metadata.py (44 KB)
Model registry and token estimation:
- `get_model_context_length()` — resolves context window from hardcoded → OpenRouter → models.dev → probing
- `estimate_messages_tokens_rough()` — char-based estimation
- `DEFAULT_CONTEXT_LENGTHS` — fallbacks for Claude, GPT, Gemini, Qwen, etc.
- `MINIMUM_CONTEXT_LENGTH` = 64K

### memory_manager.py (14 KB)
Orchestrates builtin + at most one external MemoryProvider:
- Routes tool calls via `_tool_to_provider` dict
- `prefetch_all()`, `sync_all()`, `build_system_prompt()`
- Failures in one provider never block the other

### memory_provider.py
ABC for pluggable memory providers:
- Lifecycle: `initialize()`, `system_prompt_block()`, `prefetch()`, `sync_turn()`, `get_tool_schemas()`, `handle_tool_call()`, `shutdown()`
- Optional hooks: `on_turn_start()`, `on_session_end()`, `on_pre_compress()`, `on_memory_write()`

### error_classifier.py (28 KB)
`ClassifiedError` and `FailoverReason`:
- Taxonomy: auth, billing, rate_limit, overloaded, timeout, context_overflow, model_not_found
- Each carries: `retryable`, `should_compress`, `should_rotate_credential`, `should_fallback`

### display.py (40 KB)
Terminal display formatting for CLI:
- Spinner, diff formatting, tool output rendering
- Markdown rendering in terminal

### smart_model_routing.py (8 KB)
Routes simple messages to cheaper models:
- Short messages (<160 chars, <28 words) with no code/URLs → cheap model
- Complex keywords (debug, implement, refactor) → primary model

### Other Files
| File | Purpose |
|------|---------|
| `context_engine.py` | ABC for pluggable context engines |
| `context_references.py` | @file:, @folder:, @git:, @url: expansion |
| `copilot_acp_client.py` | GitHub Copilot ACP shim |
| `insights.py` | Historical usage analytics |
| `manual_compression_feedback.py` | User-facing compression summaries |
| `models_dev.py` | models.dev API integration |
| `prompt_caching.py` | Anthropic cache_control injection |
| `rate_limit_tracker.py` | Rate limit header parsing |
| `redact.py` | Regex-based secret redaction |
| `retry_utils.py` | Jittered exponential backoff |
| `skill_commands.py` | Slash command helpers |
| `skill_utils.py` | Skill metadata utilities |
| `subdirectory_hints.py` | Progressive context discovery |
| `title_generator.py` | Auto session title generation |
| `trajectory.py` | ShareGPT format trajectory saving |
| `usage_pricing.py` | Token cost estimation and billing |
