---
name: mobiscroll-ui
description: |
  INVOKE THIS SKILL FIRST for any Mobiscroll UI work. It detects the framework and
  orchestrates the correct framework-specific sub-skill. Triggers when the user or
  codebase references Mobiscroll UI components: @mobiscroll/ imports, Mbsc-prefixed
  TypeScript types (MbscCalendarEvent, MbscEventcalendarView), mobiscroll.eventcalendar()
  calls, mbsc- element selectors, MbscModule in Angular, or Mobiscroll CSS imports.
  Also triggers for scheduling, calendar, date/time picker, and booking UIs built with
  Mobiscroll. Covers React, Angular, Vue, JavaScript, and jQuery. Activate even when
  "Mobiscroll" is not mentioned — detect from code artifacts.
---

# Mobiscroll UI Development

This skill teaches you how to write correct, idiomatic Mobiscroll code. Mobiscroll is a
UI component library for scheduling, calendars, date/time pickers, selects, and forms.
It ships separate packages per framework. The API surface is large and framework-specific —
do not guess props, events, or methods from memory.

## Step 0 — Check Installation First

Before anything else, check whether Mobiscroll is already installed:

- Look for any `@mobiscroll/*` key in `package.json` (dependencies or devDependencies).
- If found → **Mobiscroll is installed. Skip this section entirely and proceed to Step 1.**

**Only if Mobiscroll is NOT installed**, guide the user through the CLI setup:

```bash
npm install -g @mobiscroll/cli
mobiscroll config <framework>   # vue | react | angular | javascript | jquery
```

This command:
- Generates `.npmrc` with the auth token for the Mobiscroll npm registry
- Installs the correct `@mobiscroll/<framework>` package automatically

**Never manually configure `.npmrc`** — the CLI handles it. Manual `.npmrc` editing is a
last resort only for CI/CD environments where the CLI cannot run.

After the user runs `mobiscroll config`, continue to Step 1.

---

## Step 1 — Mandatory: Detect the Environment

Before writing ANY Mobiscroll code, determine the framework and version:

1. Run the `resolveEnvironment` MCP tool on the project working directory.
2. This returns the framework (`react`, `angular`, `vue`, `js`, `jquery`) and Mobiscroll
   major version (`5` or `6`).
   Note: the tool returns `js` for vanilla JavaScript — use the `mobiscroll-ui-javascript` skill.
3. If the tool is unavailable or returns nothing, check `package.json` for:
   - `@mobiscroll/react` → React
   - `@mobiscroll/angular` → Angular
   - `@mobiscroll/vue` → Vue
   - `@mobiscroll/javascript` → vanilla JavaScript
   - `@mobiscroll/jquery` → jQuery
4. If you still can't determine the framework, ask the user.

**Lock the framework for the rest of the session.** Never mix framework patterns.

## Step 2 — Mandatory: Look Up Before You Write

**NEVER write Mobiscroll code from memory.** The API is large, version-specific, and
framework-specific. Props that exist in React may not exist in Angular. Events have
different signatures across frameworks. Use the Mobiscroll **MCP server** tools — call
each by name:

1. **Look up the component schema (two steps)** — before writing any code:
   1. Call `getComponentSchema` with the component name, detected framework, and major
      version and **no `names`**. This returns a compact index of every entity
      (`name`, `kind`, `tsType`, and a one-line `summary`) — cheap to read.
   2. Review the index, then call `getComponentSchema` **again with `names: [...]`**
      (the entities you will actually use) to get their full details — complete
      description, default value, related entities. Optionally pass `kind`
      (`option` | `event` | `method` | `renderer`) to narrow. Do NOT try to pull the
      whole schema at full detail; it is large and wastes tokens.
   - For Eventcalendar, use the variant suffix: `eventcalendar.base`, `eventcalendar.agenda`,
     `eventcalendar.scheduler`, `eventcalendar.timeline`, `eventcalendar.calenderview`.
   - For other components: `datepicker`, `select`, `popup`.
   - Not sure a component name is correct? Call `listComponents` (framework +
     major version) to get the authoritative list before guessing.
2. **Search for examples** — call `searchExamples` when the user asks for a
   specific use case (e.g., "resource calendar", "date range picker", "event CRUD"). Results
   are summaries only — call `getExampleById` with the returned `exampleId`
   (plus framework and major version) to fetch the full runnable source before using or
   adapting it.
3. **Validate your output** — after writing Mobiscroll code, call `validateCode`
   using the same component ID you passed to `getComponentSchema`
   (e.g. `eventcalendar.scheduler`, not just `eventcalendar`).

This is not optional. Skipping the lookup produces hallucinated APIs that look plausible
but don't compile. The MCP server has the authoritative schema — use it. If the MCP tools
are unavailable (not connected, unreachable, or network-blocked), fall back to the
`llms-*.txt` docs for the detected framework (see Documentation Sources below); only if
those are also unavailable, rely on general knowledge — and say so explicitly, flagging
that the API should be verified. Never silently guess props, events, or methods.

## Step 3 — Load Framework Conventions

Invoke the skill matching the detected framework:

| Framework         | Skill to invoke            |
| :---------------- | :------------------------- |
| React             | `mobiscroll-ui-react`      |
| Angular           | `mobiscroll-ui-angular`    |
| Vue               | `mobiscroll-ui-vue`        |
| JavaScript (`js`) | `mobiscroll-ui-javascript` |
| jQuery            | `mobiscroll-ui-jquery`     |

Invoke only the one matching the detected framework. Apply its conventions for all
subsequent code in this session.

---

## Core Conventions (All Frameworks)

### Component Architecture

All scheduling views are part of ONE component: **Eventcalendar**. They are configured
via the `view` option — they are NOT separate components.

- Calendar grid → `view: { calendar: { type: 'month' } }`
- Agenda / event list → `view: { agenda: { type: 'month' } }`
- Scheduler (vertical) → `view: { scheduler: { type: 'week' } }`
- Timeline (horizontal) → `view: { timeline: { type: 'week' } }`
- Combined views → multiple keys in the `view` object

Never create separate component instances for different views. One Eventcalendar instance
can show calendar + agenda simultaneously by setting both keys in `view`.

### Version Awareness

This skill covers Mobiscroll v6 (current) and v5 (legacy). If you detect v5 patterns
(different API names, removed options, old import paths), do not silently translate.
Instead, inform the user:

> "This looks like Mobiscroll v5 syntax. The current version is v6. Would you like
> the v6 equivalent, or guidance using v5?"

### Documentation Sources (for deep lookups)

When the MCP tools don't cover a topic (theming, CSS customization, recurring events,
timezone handling), consult the online documentation:

- **Hub:** `https://mobiscroll.com/docs/llms.txt` — index of all framework docs
- **Full docs per framework (v6):**
  - JS: `https://mobiscroll.com/docs/llms-javascript-full.txt`
  - React: `https://mobiscroll.com/docs/llms-react-full.txt`
  - Angular: `https://mobiscroll.com/docs/llms-angular-full.txt`
  - Vue: `https://mobiscroll.com/docs/llms-vue-full.txt`
  - jQuery: `https://mobiscroll.com/docs/llms-jquery-full.txt`
- **Full docs per framework (v5 — use when `resolveEnvironment` returns `major: 5`):**
  - JS: `https://mobiscroll.com/docs/5.35.0/llms-javascript-full.txt`
  - React: `https://mobiscroll.com/docs/5.35.0/llms-react-full.txt`
  - Angular: `https://mobiscroll.com/docs/5.35.0/llms-angular-full.txt`
  - Vue: `https://mobiscroll.com/docs/5.35.0/llms-vue-full.txt`
  - jQuery: `https://mobiscroll.com/docs/5.35.0/llms-jquery-full.txt`
- **Icons:** `https://mobiscroll.com/docs/llms-icons.txt` (only when icon lookup needed)

Fetch only the file matching the detected framework. Never load multiple framework docs.

### Mobiscroll Connect vs. UI — Routing

Mobiscroll Connect is a **separate server-side product** for syncing with Google Calendar,
Outlook, and Apple Calendar via REST APIs and OAuth. It is NOT a UI component.

- If the user asks about server-side sync, OAuth, webhooks, REST endpoints → this is
  Connect. Invoke the `mobiscroll-connect` skill. Do NOT mix Connect APIs with UI
  component code.
- If the user asks about displaying calendar events in the UI → this is the Eventcalendar
  component. Use the framework docs.
- If the user mentions Google/Outlook/Apple Calendar, check context: client-side display
  is UI; server-side sync is Connect. The keyword alone doesn't determine routing.

### TypeScript Types

All Mobiscroll TypeScript types are prefixed `Mbsc` (e.g., `MbscEventcalendarView`,
`MbscCalendarEvent`, `MbscDatepickerControl`). Verify exact type names via the component
schema — do not invent type names.

### Theming

For theming and CSS questions, invoke the `mobiscroll-ui-theming` skill.

---

## What NOT to Do

1. **Never invent APIs.** If a prop, event, or method is not in the schema, say so.
   Do not guess names that "seem right."
2. **Never mix frameworks.** React JSX in an Angular project, Vue SFCs in vanilla JS,
   jQuery plugin chains in React — all of these are wrong.
3. **Never treat views as separate components.** Scheduler, Timeline, Agenda, Calendar
   are all views of Eventcalendar, configured via the `view` option.
4. **Never skip the schema lookup.** Even if you've seen similar code before, the API
   may differ between versions or frameworks. Always verify.
5. **Never mix Connect with UI.** Connect APIs (REST, OAuth, webhooks) and UI component
   APIs (props, events, rendering) are separate systems. Never blend them in one code block.
6. **Never answer from training knowledge when MCP tools are available.** The MCP server
   has the authoritative, versioned schema. Your training data may be outdated or wrong.
