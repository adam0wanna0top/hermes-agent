# Agent Core — AIAgent (`run_agent.py`)

> The `AIAgent` class in `run_agent.py` (10,871 lines, 551 KB) is the heart of the system.

## Class: AIAgent

### Constructor Parameters
- `model` — LLM model identifier
- `platform` — Source platform ("cli", "telegram", "discord", "cron", "acp", etc.)
- `enabled_toolsets` — List of toolset names to activate
- `credential_pool` — Optional `CredentialPool` for API key rotation
- `config_overrides` — Dict of config overrides
- `session_id` — Optional session ID for resuming

### Main Loop (simplified)
```python
def run(self, user_message: str) -> str:
    self.messages.append({"role": "user", "content": user_message})
    while True:
        response = self._call_llm(self.messages)
        if response.tool_calls:
            for tc in response.tool_calls:
                result = handle_function_call(tc, self.tools)
                self.messages.append({"role": "tool", "content": result})
        else:
            return response.content  # Final answer
```

### Key Methods
- `run()` / `run_streaming()` — Main conversation loop
- `_call_llm()` — Send messages to LLM provider (OpenAI-format)
- `_build_system_prompt()` — Delegate to `prompt_builder.py`
- `_handle_tool_call()` — Route to `model_tools.handle_function_call()`
- `_compress_context()` — Delegate to `ContextCompressor`
- `_stream_response()` — Streaming variant with progressive callbacks
- `save_memory()` / `load_memory()` — Memory persistence
- `reset()` / `new_session()` — Session lifecycle

### Tool Calling Flow
1. `_call_llm()` returns response with `tool_calls`
2. Each `tool_call` has `function.name` and `function.arguments`
3. `handle_function_call()` in `model_tools.py` resolves tool name → handler
4. Tool handler executes, returns string result
5. Result appended as `{"role": "tool", ...}` message
6. Loop continues until no more `tool_calls`

### Streaming
- `_stream_response()` uses SSE-style chunked response
- Callbacks: `on_tool_start`, `on_tool_result`, `on_thinking`, `on_message`
- Used by CLI for progressive display and by Gateway for message editing

### Error Recovery
- `error_classifier.py` classifies errors → `ClassifiedError` with recovery hints
- Retryable errors: automatic retry with exponential backoff
- Rate limit: credential rotation via `CredentialPool`
- Context overflow: automatic compression via `ContextCompressor`
- Auth/billing: provider failover to next in chain

### Session State
- `self.messages` — Full conversation history (OpenAI format)
- `self.session_id` — Unique session identifier
- `self.token_counts` — Running token usage tracking
- `self.tool_results` — Accumulated tool results for display
- `self.memory` — MemoryManager instance

### Integration Points
- `agent/` package — Prompt building, context compression, memory
- `tools/registry.py` — Tool discovery and execution
- `hermes_state.py` — Session persistence to SQLite
- `agent/auxiliary_client.py` — Secondary LLM calls (compression, titles)
```
