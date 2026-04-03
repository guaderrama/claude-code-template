# Claude Code Native Commands

> Comandos nativos de Claude Code que no necesitan skills personalizados.

---

## Session & Context

| Command | Description |
|---------|-------------|
| `/compact [focus]` | Compress conversation keeping focus topic. Use when context gets large. |
| `/clear` | Clear conversation and start fresh. |
| `/resume` | Resume last conversation from where it left off. |
| `/cost` | Show token usage and cost for current session. |
| `/model [name]` | Switch model mid-conversation (opus, sonnet, haiku). |
| `/fast` | Toggle fast mode (same model, faster output). |
| `/effort [level]` | Set reasoning effort: low, medium, high. |

## Memory & Learning

| Command | Description |
|---------|-------------|
| `/memory` | View and manage Claude's auto-memory entries. |
| `/dream` | Trigger dream mode — Claude reviews past sessions and extracts learnings. |
| `/btw [note]` | Add a side note without changing the current task context. |

## Planning & Review

| Command | Description |
|---------|-------------|
| `/plan` | Enter plan mode — structured planning before implementation. |
| `/review` | Review recent changes for quality, bugs, and improvements. |
| `/security-review` | Security-focused review of recent code changes. |

## Execution

| Command | Description |
|---------|-------------|
| `/batch [files]` | Process multiple files with the same operation. |
| `/loop [interval] [cmd]` | Run a command on a recurring interval (e.g., `/loop 5m /review`). |

## Help

| Command | Description |
|---------|-------------|
| `/help` | Show all available commands and their descriptions. |
| `/config` | View and modify Claude Code settings. |

---

## Tips

- **Context large?** Use `/compact` with a focus: `/compact authentication flow`
- **Switching tasks?** `/clear` starts fresh, `/compact` preserves key context
- **Cost tracking**: `/cost` shows cumulative tokens — use to monitor expensive sessions
- **Model routing**: `/model haiku` for quick tasks, `/model opus` for complex reasoning
- **Auto-memory**: Enabled in settings — Claude remembers across sessions automatically
- **Auto-dream**: Enabled in settings — Claude consolidates learnings between sessions
