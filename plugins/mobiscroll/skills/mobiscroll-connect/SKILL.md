---
name: mobiscroll-connect
description: |
  Mobiscroll Connect — server-side OAuth 2.0 calendar sync with Google, Microsoft
  365/Outlook.com, Apple Calendar, and CalDAV via REST API or SDKs (Node, Python,
  PHP, .NET, Java, Go, Ruby). Triggers on: Mobiscroll Connect, calendar OAuth,
  syncing/reading/writing Google or Outlook or Apple calendar events server-side,
  calendar webhooks, MobiscrollConnectClient. This is a separate backend product
  from the UI components — it has no UI. For rendering events in a calendar/scheduler
  component, use mobiscroll-ui instead. Activate even when "Mobiscroll" is not
  mentioned — detect from OAuth/webhook/multi-provider-calendar-sync intent.
---

# Mobiscroll Connect

Mobiscroll Connect is a server-side product: a full-fledged OAuth 2.0 provider that
unifies Google Calendar, Microsoft 365/Outlook.com, Apple Calendar, and CalDAV behind
one REST API and a set of language SDKs. It has **no UI components** — it only
authenticates, syncs, and normalizes calendar data. If the user wants to *display*
events in a calendar/scheduler/agenda, that's the separate `mobiscroll-ui` skill.

## Step 0 — Detect the SDK / Integration Style

Read the project's dependency manifest yourself (`package.json`, `pyproject.toml`,
`composer.json`, `*.csproj`, `pom.xml`, `go.mod`, or `Gemfile` — the MCP server runs
over HTTP and cannot read local files) and call the `resolveConnectEnvironment` MCP tool
with that content via `manifestContent` to detect which Connect SDK language/version the
project already depends on.

If no Connect SDK dependency is found, ask the user whether they want a specific
language SDK or raw REST calls, and use the setup table below to get them started.

## Step 1 — Mandatory: Look Up Before You Write

**Never invent Connect endpoints, SDK methods, or parameters from memory.** Look
everything up with the Mobiscroll **MCP server** tools — call each by name:

- **REST work** → call `listConnectEndpoints`, then `getConnectEndpointSchema` (by
  `endpointId`, or by `method` + `path`) for full parameter/response detail before
  writing a request.
- **SDK work** → call `listConnectSdkMethods` (by `language`), then `getConnectSdkMethod`
  for the exact signature, return type, and example before writing a call.
- **Translating** a REST example to SDK code, or vice versa → call `mapConnectEndpointToSdk`.
- **Open-ended "how do I..." questions** → call `searchConnect` (space-separated
  keywords, AND logic) to find the relevant endpoint or SDK method.
- **Error handling code** → call `getConnectErrorTaxonomy` (optionally filtered by
  `language`) for the idiomatic exception type instead of guessing status codes or
  exception names.

This is not optional — the MCP server has the authoritative, versioned schema. If the
MCP tools are unavailable (not connected, unreachable, or network-blocked), fall back to
`https://mobiscroll.com/docs/llms-connect-full.txt`; only if that is also unavailable,
rely on general knowledge — and say so explicitly, flagging that the API should be
verified. Never silently guess endpoints or SDK methods.

## Setup & Credentials

1. Create a Connect application in the Mobiscroll Connect dashboard.
2. Configure a **redirect URI** (must exactly match what's passed to `/oauth/authorize`)
   and, optionally, a **webhook URL**.
3. Retrieve **Client ID** (public) and **Client Secret** (confidential) from
   Settings → API Credentials.
4. Store credentials server-side only (e.g. `.env`) — never in frontend/client code.
5. **Create a separate Connect application per environment** (development, staging,
   production), each with its own Client ID, secret, redirect URI, and webhook URL.

## SDK Setup by Language

| Language | Install | Import | Client |
|:---------|:--------|:-------|:-------|
| Node.js | `npm install @mobiscroll/connect-sdk` | `import { MobiscrollConnectClient } from "@mobiscroll/connect-sdk"` | `new MobiscrollConnectClient({ clientId, clientSecret, redirectUri })` |
| Python | `pip install mobiscroll-connect-sdk` | `from mobiscroll_connect import MobiscrollConnectClient` | `MobiscrollConnectClient(client_id=..., client_secret=..., redirect_uri=...)` — also supports `with ... as client:` and an async client via `mobiscroll_connect.aio` |
| PHP | `composer require mobiscroll/connect-php` | `use Mobiscroll\Connect\MobiscrollConnectClient;` | `new MobiscrollConnectClient(clientId: ..., clientSecret: ..., redirectUri: ...)` |
| .NET | `dotnet add package Mobiscroll.Connect` | `using Mobiscroll.Connect;` | `new MobiscrollConnectClient(clientId: ..., clientSecret: ..., redirectUri: ...)` (targets .NET 8+) |
| Java | Maven `com.mobiscroll:connect-sdk` / Gradle `implementation("com.mobiscroll:connect-sdk:<version>")` | `import com.mobiscroll.connect.MobiscrollConnectClient;` | `new MobiscrollConnectClient(clientId, clientSecret, redirectUri)` — or `MobiscrollConnectConfig.builder()...build()` for advanced config |
| Go | `go get github.com/acidb/mobiscroll-connect-sdks/sdks/go@latest` | `import mobiscroll "github.com/acidb/mobiscroll-connect-sdks/sdks/go"` | `mobiscroll.NewClient(clientID, clientSecret, redirectURI, opts...)` |
| Ruby | `gem 'mobiscroll-connect'` (Gemfile) or `gem install mobiscroll-connect` | `require 'mobiscroll-connect'` | `Mobiscroll::Connect::Client.new(client_id:, client_secret:, redirect_uri:)` |

This table is for initial setup only — verify exact method signatures via the
`getConnectSdkMethod` MCP tool before writing API calls.

## Core Concepts

- **OAuth flow**: `GET /oauth/authorize` (params `client_id`, `user_id`, `scope`,
  `redirect_uri`) → provider picker UI (Google/Microsoft/Apple/CalDAV) → provider
  login & consent → redirect back with an authorization code → backend
  `POST /oauth/token` (HTTP Basic `base64(clientId:clientSecret)` + a `CLIENT_ID`
  header, form-urlencoded body) → `access_token` + `refresh_token`. Refresh with
  `POST /oauth/token` (`grant_type=refresh_token`); revoke with `POST /oauth/disconnect`.
- **Multiple accounts per user**: a user can connect more than one provider account.
  `GET /oauth/connection-status` lists the connected accounts grouped by provider;
  `POST /oauth/disconnect` removes one account or all accounts of a provider. The SDK
  `setCredentials` method caches a token set on the client for later calls (local, no
  network request).
- **Scopes** (control what `GET /events` returns): `read-write` (full read/write),
  `read` (read-only), `free-busy` (anonymized availability only — use it for
  availability / double-booking checks without exposing event details).
- **Core resources**: Calendars (`GET /calendars`) and Events (`GET /events`,
  `POST /event`, `PUT /event`, `DELETE /event`), with `updateMode`/`deleteMode` of
  `this`/`following`/`all` for recurring series, `singleEvents` to expand RRULE
  recurrence into instances, and pagination via `pageSize`/`nextPageToken`. Querying
  `GET /events` across providers (especially with the `free-busy` scope) is how you
  check availability and prevent double-booking.
- **Webhooks** (not modeled by the MCP tools — consult the llms-connect docs for the
  exact schema): configure a **Webhook URL** in your Connect application settings, then
  subscribe/unsubscribe calendars via `POST /subscribe-webhook` and
  `POST /unsubscribe-webhook`. The payload reports the change type and the affected
  provider/calendar/event.
- **Hosted Connect pages**: the provider-picker, login, and consent screens are hosted
  by Connect and are customizable (Branding) and localizable — configured in the
  dashboard, not in your code. See the llms-connect docs.
- **Data normalization**: events from every provider come back in one unified
  `CalendarEvent` shape, sorted chronologically, with timezones normalized.

## Per-language Call-Site Idioms

Every SDK exposes the same operations, but call-site idioms differ. Verify exact
signatures with the `getConnectSdkMethod` MCP tool — do **not** hardcode them (they drift
between versions):

| Language | Idiom |
|:---------|:------|
| Node | async — `await client.events.create(...)`; methods return Promises |
| .NET | async — `…Async` methods (`await client.Events.CreateAsync(...)`), optional `CancellationToken` |
| Go | `func(ctx, ...) (T, error)` — pass a `context.Context`, check the returned `err` |
| Java | synchronous, typed request/response objects |
| Python | synchronous (dict / keyword args); optional async client via `mobiscroll_connect.aio`; `iter_all` auto-paginates |
| PHP | synchronous, associative arrays |
| Ruby | synchronous, **keyword arguments** (not a single options object) |

Two behaviors hold across all SDKs:
- `setCredentials` / `set_credentials` caches a token set on the client locally — no network call.
- On an expired access token the SDK auto-refreshes once and retries the call before
  surfacing an authentication error.

## Connect vs. UI — Routing

Mobiscroll Connect (this skill) and Mobiscroll's UI components (`mobiscroll-ui`) are
separate systems — never blend them in one code block:

- Server-side sync, OAuth, webhooks, REST/SDK calls → this skill.
- Displaying events in a calendar, scheduler, timeline, or agenda → stop and invoke
  the `mobiscroll-ui` skill instead.
- A typical full-stack setup uses both: this skill to fetch/sync events from
  Google/Outlook/Apple into your backend, then passes the normalized events as plain
  data into an Eventcalendar instance built with `mobiscroll-ui`.

## What NOT to Do

1. **Never invent endpoints, SDK methods, or parameters.** Look them up via the MCP
   tools above.
2. **Never put the Client Secret in frontend/client-side code.** Token exchange and
   any use of the secret happen server-side only.
3. **Never reuse one Connect application/credential set across environments.** Each
   of dev/staging/production needs its own app.
4. **Never skip token-refresh handling.** A `401 Unauthorized` means the access token
   needs refreshing (or the user needs to re-authenticate) — not a hardcoded retry.
5. **Never mix Connect calls into Eventcalendar/UI rendering code.** Keep the sync
   layer (this skill) and the display layer (`mobiscroll-ui`) separate.

## Documentation Sources (for topics beyond the MCP tools)

For topics the MCP tools don't cover — dashboard branding, localization, the Postman
collection, or general best practices — fetch:
`https://mobiscroll.com/docs/llms-connect-full.txt`
