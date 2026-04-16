# LLM Integration Layer

> Spread across `agent/` files — the unified multi-provider LLM system.

## Provider Resolution Chain

### Primary Provider (for agent conversations)
Resolved at agent creation time based on available credentials:
1. OpenRouter (`OPENROUTER_API_KEY`)
2. Nous Portal (`~/.hermes/auth.json`)
3. Custom endpoint (`config.yaml model.base_url`)
4. Anthropic native (`ANTHROPIC_API_KEY`)
5. Direct providers (GLM/z.ai, Kimi/Moonshot, MiniMax, Google, DeepSeek)

### Auxiliary Provider (for compression, titles, vision)
`auxiliary_client.py` resolution chain:
1. OpenRouter → 2. Nous Portal → 3. Custom → 4. Codex OAuth → 5. Anthropic → 6. Direct
- Automatic failover on 402 (credit exhaustion)

## Supported Providers

| Provider | Auth Method | Models |
|----------|-------------|--------|
| OpenRouter | API key | 100+ models (aggregator) |
| Anthropic | API key / OAuth | Claude Opus 4.6, Sonnet 4.6, Haiku 4.5 |
| OpenAI / Codex | API key / OAuth | GPT-4o, o3, o4-mini |
| Google Gemini | API key / OpenRouter | Gemini 2.5 Pro/Flash |
| DeepSeek | API key | DeepSeek V3/R1 |
| z.ai / GLM | API key | GLM models |
| Kimi / Moonshot | API key | Moonshot models |
| MiniMax | API key | MiniMax models |
| Custom | base_url + key | Any OpenAI-compatible API |

## Key Abstractions

### CredentialPool (`agent/credential_pool.py`)
- Manages multiple API keys per provider for failover
- Strategies: fill_first, round_robin, random, least_used
- Exhausted credentials cool down after 1 hour (429/402 errors)
- Supports OAuth tokens and API keys

### Anthropic Adapter (`agent/anthropic_adapter.py`)
- Translates OpenAI-format messages ↔ Anthropic Messages API
- Handles thinking budget (xhigh=32K down to none)
- Model-specific output limits (Claude 4.6 = up to 128K)
- Beta headers: interleaved-thinking, fine-grained-tool-streaming, fast-mode

### Model Metadata (`agent/model_metadata.py`)
- Context window resolution: hardcoded → OpenRouter API → models.dev → probing
- Token estimation: char-based rough estimation
- Provider prefix stripping: `openrouter:model`, `anthropic:model`
- MINIMUM_CONTEXT_LENGTH = 64K (models below rejected)

### Error Classification (`agent/error_classifier.py`)
Taxonomy with recovery hints:
- `auth` → rotate credential
- `billing` → failover provider
- `rate_limit` → retry with backoff + rotate credential
- `overloaded` → retry with backoff
- `context_overflow` → compress context
- `timeout` → retry
- `model_not_found` → failover

### Usage & Pricing (`agent/usage_pricing.py`)
- `CanonicalUsage`: input_tokens, output_tokens, cache_read/write, reasoning_tokens
- Cost estimation per provider with multiple pricing sources
- Billing route tracking per session

## API Call Flow
```
AIAgent._call_llm()
    → CredentialPool.get_credential()
    → anthropic_adapter.call() (if Anthropic)
        OR direct OpenAI-format call
    → Response parsed → ClassifiedError on failure
    → Recovery action based on classification
```
