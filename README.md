# Mobiscroll Marketplace for Claude Code

A Claude Code plugin marketplace that provides AI coding assistance for [Mobiscroll](https://mobiscroll.com) — a UI component library for calendars, schedulers, date/time pickers, selects, and forms — and for Mobiscroll Connect, its server-side calendar sync product.

## Plugins

| Plugin | Description |
|:-------|:------------|
| [`mobiscroll`](plugins/mobiscroll/README.md) | Skills + MCP server for writing Mobiscroll UI code across all frameworks, plus Mobiscroll Connect (server-side OAuth calendar sync) |

The `mobiscroll` plugin includes:
- **Eight skills** — conventions, patterns, and anti-patterns for React, Angular, Vue, JavaScript, jQuery, and theming, plus a standalone skill for Mobiscroll Connect
- **MCP server** — live API schema lookup, code validation, and example search against the Mobiscroll docs, plus a matching set of tools for Mobiscroll Connect's REST endpoints and SDKs

## Installation

> **Note:** The exact command syntax may vary depending on your Claude Code version. Check the [Claude Code docs](https://docs.anthropic.com/claude-code) for the latest plugin commands if the steps below don't work.

**Step 1 — Register this marketplace (one-time):**

```bash
/plugin marketplace add acidb/mobiscroll-marketplace
```

**Step 2 — Install the plugin:**

```bash
/plugin install mobiscroll@mobiscroll
```

## Plugin Documentation

See [`plugins/mobiscroll/README.md`](plugins/mobiscroll/README.md) for full details on what the plugin does, how it works, and supported frameworks and components.
