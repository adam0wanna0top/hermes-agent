# CLI (`cli.py`)

> `HermesCLI` class (10,017 lines, 446 KB) — interactive terminal UI.

## Key Features
- Rich TUI built on `prompt_toolkit`
- Multiline editing with syntax highlighting
- Slash command autocomplete
- Conversation history (persistent)
- Streaming tool output with spinner
- Skin/theme engine
- Shell completion (bash/zsh/fish)

## Slash Commands
- `/help` — show help
- `/model [name]` — switch or list models
- `/tools` — list available tools
- `/reset` — reset conversation
- `/compact` — compress context
- `/memory` — view/edit memory
- `/skill [name]` — load/view skills
- `/config` — view/edit configuration
- `/quit` — exit

## Display System (`agent/display.py`, 40KB)
- Spinner with multiple styles
- Diff formatting for file edits
- Tool output rendering
- Markdown rendering in terminal
- Rich console integration

## Skin Engine (`hermes_cli/skin_engine.py`, 39KB)
- Customizable themes and colors
- Multiple built-in skins
- User-defined skin support

## Streaming
- Progressive response display
- Tool execution progress
- Thinking/reasoning display
- Interrupt support (Ctrl+C)

## Shell Completion (`hermes_cli/`)
- bash, zsh, fish support
- Dynamic completion for models, tools, skills
- Profile name completion
