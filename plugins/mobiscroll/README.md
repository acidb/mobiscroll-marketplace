# Mobiscroll Plugin for Claude Code

Claude Code plugin that provides Mobiscroll UI development assistance through a skill
(conventions, patterns, anti-patterns) and an MCP server (live API schema lookup,
code validation, example search).

## What's Included

- **Skills: `mobiscroll-ui` + framework sub-skills** — `mobiscroll-ui` is the orchestrator:
  it detects the framework and delegates to the matching sub-skill (`mobiscroll-ui-react`,
  `mobiscroll-ui-angular`, `mobiscroll-ui-vue`, `mobiscroll-ui-javascript`, `mobiscroll-ui-jquery`).
  Theming questions are handled by `mobiscroll-ui-theming`. All seven skills are installed
  together when you install the plugin.
- **MCP Server: `mobiscroll`** — Live API tools for schema lookup, code validation,
  example search, and environment detection.

## Installation

> **Note:** The exact command syntax may vary depending on your Claude Code version. Check the [Claude Code docs](https://docs.anthropic.com/claude-code) for the latest plugin commands if the steps below don't work.

**Step 1 — Register the marketplace (one-time):**

```bash
/plugin marketplace add acidb/mobiscroll-marketplace
```

**Step 2 — Install the plugin:**

```bash
/plugin install mobiscroll@mobiscroll
```

Or install directly from a local clone during development:

```bash
/plugin install ./plugins/mobiscroll
```

## How It Works

When you ask Claude Code to write Mobiscroll code, the skill:

1. **Detects your framework** — runs `resolveEnvironment` to find whether you're using
   React, Angular, Vue, vanilla JS, or jQuery, and which Mobiscroll version.
2. **Loads framework conventions** — invokes the matching framework sub-skill so Claude writes
   idiomatic code for your framework.
3. **Looks up the API** — calls `getComponentSchema` before writing any props, events,
   or methods, ensuring nothing is hallucinated.
4. **Validates output** — runs `validateCode` on generated snippets to catch errors
   before they reach you.

## Plugin Structure

```
mobiscroll/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── .mcp.json                    # MCP server configuration
├── skills/
│   ├── mobiscroll-ui/
│   │   └── SKILL.md             # Core orchestrator — detects framework, delegates
│   ├── mobiscroll-ui-react/
│   │   └── SKILL.md
│   ├── mobiscroll-ui-angular/
│   │   └── SKILL.md
│   ├── mobiscroll-ui-vue/
│   │   └── SKILL.md
│   ├── mobiscroll-ui-javascript/
│   │   └── SKILL.md
│   ├── mobiscroll-ui-jquery/
│   │   └── SKILL.md
│   └── mobiscroll-ui-theming/
│       └── SKILL.md
└── README.md
```

## Supported Frameworks

| Framework  | Package                  |
|:-----------|:-------------------------|
| React      | `@mobiscroll/react`      |
| Angular    | `@mobiscroll/angular`    |
| Vue        | `@mobiscroll/vue`        |
| JavaScript | `@mobiscroll/javascript` |
| jQuery     | `@mobiscroll/jquery`     |

## Supported Components

- **Eventcalendar** — Calendar, Scheduler, Timeline, Agenda views
- **Datepicker** — Date, time, and date-range selection
- **Select** — Dropdown select with search, grouping, multi-select
- **Popup** — Modal/overlay container
- **Forms** — Input, textarea, button, and form elements
- **Notifications** — Toast, snackbar, alert
