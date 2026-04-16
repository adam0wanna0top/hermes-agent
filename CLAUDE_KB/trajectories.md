# Trajectory System

> ShareGPT-format conversation trajectories for RL training data.

## Files
| File | Purpose |
|------|---------|
| `agent/trajectory.py` | Static helpers, file-write logic |
| `trajectory_compressor.py` (1463 lines) | Post-processing compression |
| `batch_runner.py` (1287 lines) | Parallel batch processing |
| `mini_swe_runner.py` (709 lines) | SWE-bench evaluation |

## ShareGPT Format
Standard role conventions:
- `system` — system prompt
- `human` — user message
- `gpt` — assistant response (includes tool calls)
- `tool` — tool execution result

Normalization:
- Reasoning content → `think` tags
- Tool calls → XML-wrapped JSON
- Tool responses → grouped into single turns

## Trajectory Compression (`trajectory_compressor.py`)
1. Protect first turns (system, human, first gpt, first tool)
2. Protect last N turns (final actions and conclusions)
3. Compress only middle turns (starting from 2nd tool response)
4. Replace compressed region with single human summary (LLM-generated)
5. Keep remaining tool calls intact

Configurable:
- Compression model (default: `google/gemini-3-flash-preview` via OpenRouter)
- Concurrency (default: 50 parallel API calls)
- Per-trajectory timeout (default: 5 minutes)
- Token counting via HuggingFace tokenizers

## Batch Runner (`batch_runner.py`)
- Parallel trajectory processing for RL data generation
- Multiple task prompts per run
- Checkpoint support for resuming
- Tool statistics tracking
