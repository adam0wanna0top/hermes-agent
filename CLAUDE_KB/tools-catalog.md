# Tools Catalog

## Browser Tools
| Tool | File | Purpose |
|------|------|---------|
| `browser_navigate` | `browser_tool.py` (93KB) | Navigate to URL, take screenshot |
| `browser_click` | | Click element |
| `browser_type` | | Type text into element |
| `browser_extract` | | Extract page content |
| `browser_screenshot` | | Capture page screenshot |
| `browser_scroll` | | Scroll page |
| `browser_wait` | | Wait for element/condition |
- Providers: Browserbase, Playwright, Firecrawl
- Anti-detection: Camofox provider (`browser_camofox.py`)

## Terminal Tools
| Tool | File | Purpose |
|------|------|---------|
| `terminal` | `terminal_tool.py` (74KB) | Execute shell commands |
- 6 backends in `tools/environments/`: local, Docker, SSH, Modal, Daytona, Singularity
- Configurable: timeout, working directory, shell, sudo

## Web Tools
| Tool | File | Purpose |
|------|------|---------|
| `web_search` | `web_tools.py` (87KB) | Search via Exa, Parallel, or Firecrawl |
| `web_extract` | | Extract content from URL |
| `web_crawl` | | Crawl website pages |

## File Tools
| Tool | File | Purpose |
|------|------|---------|
| `file_read` | `file_operations.py` (47KB) | Read file contents |
| `file_write` | | Write/create files |
| `file_search` | `file_tools.py` (37KB) | Search files by pattern (grep/glob) |
| `file_edit` | | Edit files with string replacement |

## Code Execution
| Tool | File | Purpose |
|------|------|---------|
| `code_execute` | `code_execution_tool.py` (53KB) | Run code in sandbox (Python, JS, etc.) |

## MCP Integration
| Tool | File | Purpose |
|------|------|---------|
| (dynamic) | `mcp_tool.py` (87KB) | MCP server tools discovered at runtime |
- Supports stdio and HTTP transports
- Dynamic tool discovery via `notifications/tools/list_changed`

## Skills Tools
| Tool | File | Purpose |
|------|------|---------|
| `skills_list` | `skills_tool.py` (51KB) | List available skills |
| `skill_view` | `skill_view` | View skill details (progressive disclosure) |
| `skill_install` | `skills_hub.py` (112KB) | Install from Skills Hub |
| `skill_create` | `skill_manager_tool.py` | Create/edit/delete skills |
| `skill_search` | `skills_hub.py` | Search external skill sources |

## Memory Tools
| Tool | File | Purpose |
|------|------|---------|
| `memory` | `memory_tool.py` (585 lines) | Save/recall agent memory |
| `user` | | Save/recall user profile |
| (provider-specific) | `plugins/memory/` | External memory provider tools |

## Communication Tools
| Tool | File | Purpose |
|------|------|---------|
| `send_message` | `send_message_tool.py` (47KB) | Send to any connected platform |
| `session_search` | `session_search_tool.py` (23KB) | Search session history (FTS5) |

## Delegation Tools
| Tool | File | Purpose |
|------|------|---------|
| `delegate` | `delegate_tool.py` (46KB) | Delegate task to sub-agent |

## Vision & Media
| Tool | File | Purpose |
|------|------|---------|
| `vision_analyze` | `vision_tools.py` (31KB) | Analyze images |
| `image_generate` | `image_generation_tool.py` (27KB) | Generate images (FAL.ai) |
| `text_to_speech` | `tts_tool.py` (40KB) | TTS conversion |
| `transcribe` | `transcription_tools.py` | Audio transcription |

## Scheduling
| Tool | File | Purpose |
|------|------|---------|
| `cron_create` | `cronjob_tools.py` | Create scheduled task |
| `cron_list` | | List scheduled tasks |
| `cron_remove` | | Remove scheduled task |
| `cron_trigger` | | Trigger task manually |

## Security Tools
| Tool | File | Purpose |
|------|------|---------|
| `url_safety` | `url_safety.py` | SSRF protection (blocks private IPs) |
| (approval) | `approval.py` (37KB) | Dangerous command approval system |

## Process Management
| Tool | File | Purpose |
|------|------|---------|
| `process_*` | `process_registry.py` (48KB) | Background process lifecycle |

## Checkpoint
| Tool | File | Purpose |
|------|------|---------|
| `checkpoint_*` | `checkpoint_manager.py` | Conversation checkpointing |
