# Mobiscroll Plugin for Claude Code & Codex

Claude Code and Codex plugin that provides Mobiscroll UI development assistance through a skill
(conventions, patterns, anti-patterns) and an MCP server (live API schema lookup,
code validation, example search), plus a separate skill and MCP tools for Mobiscroll
Connect (server-side OAuth calendar sync).

## What's Included

- **Skills: `mobiscroll-ui` + framework sub-skills** — `mobiscroll-ui` is the orchestrator:
  it detects the framework and delegates to the matching sub-skill (`mobiscroll-ui-react`,
  `mobiscroll-ui-angular`, `mobiscroll-ui-vue`, `mobiscroll-ui-javascript`, `mobiscroll-ui-jquery`).
  Theming questions are handled by `mobiscroll-ui-theming`.
- **Skill: `mobiscroll-connect`** — a standalone skill for Mobiscroll Connect, the
  separate server-side OAuth calendar sync product. It triggers independently of the
  UI orchestrator (Connect work often happens in backend-only projects) and covers
  REST endpoints and SDKs for Node, Python, PHP, .NET, Java, Go, and Ruby.
  All eight skills are installed together when you install the plugin.
- **MCP Server: `mobiscroll`** — Live API tools for schema lookup, code validation,
  example search, and environment detection, plus a matching set of Connect tools
  (endpoint/SDK schema lookup, REST-to-SDK mapping, error taxonomy).

## Installation

Same commands work in both Claude Code and Codex.

> **Note:** Exact command syntax may vary by version. Check the [Claude Code docs](https://docs.anthropic.com/claude-code) or the [Codex docs](https://learn.chatgpt.com/docs/plugins) for the latest plugin commands if the steps below don't work.

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

When you ask Claude Code or Codex to write Mobiscroll code, the skill:

1. **Detects your framework** — runs `resolveEnvironment` to find whether you're using
   React, Angular, Vue, vanilla JS, or jQuery, and which Mobiscroll version.
2. **Loads framework conventions** — invokes the matching framework sub-skill so Claude writes
   idiomatic code for your framework.
3. **Looks up the API** — calls `getComponentSchema` before writing any props, events,
   or methods, ensuring nothing is hallucinated.
4. **Validates output** — runs `validateCode` on generated snippets to catch errors
   before they reach you.

For Mobiscroll Connect work, the `mobiscroll-connect` skill triggers independently
(no framework detection needed) and follows the same look-up-before-you-write
approach using its own MCP tools: `resolveConnectEnvironment`, `listConnectEndpoints`
/ `getConnectEndpointSchema`, `listConnectSdkMethods` / `getConnectSdkMethod`,
`mapConnectEndpointToSdk`, `searchConnect`, and `getConnectErrorTaxonomy`.

## Plugin Structure

```
mobiscroll/
├── .claude-plugin/
│   └── plugin.json              # Claude Code plugin manifest
├── .codex-plugin/
│   └── plugin.json              # Codex plugin manifest
├── .mcp.json                    # MCP server configuration (shared by both plugin manifests)
├── skills/                      # shared by both plugin manifests
│   ├── mobiscroll-ui/
│   │   └── SKILL.md             # Orchestrator — auto-triggers, detects framework, delegates
│   ├── mobiscroll-ui-react/
│   │   └── SKILL.md             # Sub-skill — invoked by mobiscroll-ui only, no auto-trigger
│   ├── mobiscroll-ui-angular/
│   │   └── SKILL.md             # Sub-skill — invoked by mobiscroll-ui only, no auto-trigger
│   ├── mobiscroll-ui-vue/
│   │   └── SKILL.md             # Sub-skill — invoked by mobiscroll-ui only, no auto-trigger
│   ├── mobiscroll-ui-javascript/
│   │   └── SKILL.md             # Sub-skill — invoked by mobiscroll-ui only, no auto-trigger
│   ├── mobiscroll-ui-jquery/
│   │   └── SKILL.md             # Sub-skill — invoked by mobiscroll-ui only, no auto-trigger
│   ├── mobiscroll-ui-theming/
│   │   └── SKILL.md             # Framework-agnostic sub-skill — invoked by mobiscroll-ui only, no auto-trigger
│   └── mobiscroll-connect/
│       └── SKILL.md             # Standalone — auto-triggers independently of mobiscroll-ui
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

## Mobiscroll Connect

A separate, server-side product for OAuth calendar sync — no UI, backend only.

| Supported Providers        | Supported SDK Languages |
|:----------------------------|:-------------------------|
| Google Calendar              | Node.js                  |
| Microsoft 365 / Outlook.com  | Python                   |
| Apple Calendar                | PHP                       |
| CalDAV-compatible services   | .NET, Java, Go, Ruby     |

REST API is also available directly for any language/environment not covered by an
SDK. See the `mobiscroll-connect` skill for setup, OAuth flow, and endpoint/SDK
lookup via the MCP server's Connect tools.
