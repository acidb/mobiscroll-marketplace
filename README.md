# Mobiscroll Marketplace for Claude Code & Codex

A plugin marketplace that provides AI coding assistance for [Mobiscroll](https://mobiscroll.com) — a UI component library for calendars, schedulers, date/time pickers, selects, and forms — and for Mobiscroll Connect, its server-side calendar sync product. Supports both Claude Code and OpenAI Codex.

## Plugins

| Plugin | Description |
|:-------|:------------|
| [`mobiscroll`](plugins/mobiscroll/README.md) | Skills + MCP server for writing Mobiscroll UI code across all frameworks, plus Mobiscroll Connect (server-side OAuth calendar sync) |

The `mobiscroll` plugin includes:
- **Eight skills** — conventions, patterns, and anti-patterns for React, Angular, Vue, JavaScript, jQuery, and theming, plus a standalone skill for Mobiscroll Connect
- **MCP server** — live API schema lookup, code validation, and example search against the Mobiscroll docs, plus a matching set of tools for Mobiscroll Connect's REST endpoints and SDKs

## Installation

Registration and install commands differ slightly between the two tools — pick the section that matches your setup.

> **Note:** Exact command syntax may vary by version. Check the [Claude Code docs](https://docs.anthropic.com/claude-code) or the [Codex docs](https://learn.chatgpt.com/docs/plugins) for the latest plugin commands if the steps below don't work.

### Claude Code

**Step 1 — Register this marketplace (one-time):**

```bash
/plugin marketplace add acidb/mobiscroll-marketplace
```

**Step 2 — Install the plugin:**

```bash
/plugin install mobiscroll@mobiscroll
```

### Codex

**Step 1 — Register this marketplace (one-time):**

```bash
/plugins marketplace add acidb/mobiscroll-marketplace
```

**Step 2 — Install the plugin:**

```bash
/plugins install mobiscroll@mobiscroll
```

## Plugin Documentation

See [`plugins/mobiscroll/README.md`](plugins/mobiscroll/README.md) for full details on what the plugin does, how it works, and supported frameworks and components.
