# Changelog

All notable changes to the Mobiscroll plugin marketplace will be documented in this file.

The format is based on [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.1] - 2026-09-17

### Added

- Experimental Codex support alongside Claude Code: a `.codex-plugin/plugin.json` manifest referencing the existing skills and MCP config, plus a native `.agents/plugins/marketplace.json` registration as a fallback. Additive only — no existing Claude Code files were touched.
- Apache License 2.0 added to the project.

### Changed

- `mobiscroll-ui` skill's component-schema lookup now uses a two-step index-then-detail flow (an index call to `getComponentSchema`, followed by a `names: [...]` detail call), matching a token-efficient change in the MCP server. Updated across all framework skills.
- The environment-detection step now sends the nearest `package.json` as `packageJsonContent` (the remote MCP server can't read local files), retries on a `PACKAGE_JSON_REQUIRED` response, and only falls back to `framework` + `noPackageJsonReason` after confirming no `package.json` exists.
- Root and plugin READMEs now document separate installation steps for Claude Code (`/plugin ...`) and Codex (`/plugins ...`), correcting a previous claim that the same commands work for both.

### Fixed

- Invalid `policy.authentication` value (`ON_FIRST_USE`) in the Codex `marketplace.json`, which Codex's loader rejects; switched to `ON_USE` to preserve the original intent of deferring auth until the plugin is actually invoked.

### Removed

- Redundant `version` field from the plugin entry in `.claude-plugin/marketplace.json`. The plugin's own `plugin.json` is now the single source of truth for its version.

## [1.1.0] - 2026-07-09

### Added

- New `mobiscroll-connect` skill covering server-side OAuth calendar sync (Google, Microsoft 365/Outlook.com, Apple Calendar, CalDAV) via the REST API and the Node, Python, PHP, .NET, Java, Go, and Ruby SDKs — including OAuth flow, scopes, multi-account linking, availability/double-booking, and webhooks.
- Installation check step in the `mobiscroll-ui` skill: detect a missing Mobiscroll install and use the CLI (`mobiscroll config <framework>`) instead of falling back to manual `.npmrc` configuration.

### Changed

- Hardened MCP guidance across all framework skills (React, Angular, Vue, JavaScript, jQuery): MCP tools are now referenced by base name with explicit "MCP server tool" framing, so the skills read correctly across clients (plugin install, standalone MCP, Copilot, Cursor).
- Added a graceful-degradation clause to the UI skills: when MCP tools are unavailable, fall back to the llms-*.txt docs, then to general knowledge with disclosure, rather than silently guessing.
- Added genuinely JavaScript-specific anti-pattern guidance (`setOptions` vs re-init, `destroy()` on teardown, init-after-DOM).
- Clarified the theming skill's MCP coverage: theming options are modeled in `getComponentSchema` and theme demos in `searchExamples`; CSS custom-property and Sass variable names are not modeled and must be verified against the docs rather than invented.
- Plugin description and marketplace keywords updated to mention Mobiscroll Connect.

## [1.0.0] - 2026-05-21

### Added

- Initial release of the Mobiscroll plugin marketplace for Claude Code.
- `mobiscroll-ui` orchestrator skill plus framework-specific skills for React, Angular, Vue, JavaScript, and jQuery, covering Eventcalendar, Datepicker, Select, Popup, Forms, and Notifications.
- MCP server integration for live API schema lookup, code validation, and example search against the Mobiscroll docs.
