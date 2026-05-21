---
name: mobiscroll-ui-javascript
description: |
  Mobiscroll JavaScript patterns — imperative initialization, render functions, dynamic
  updates, @mobiscroll/javascript imports. Invoked by mobiscroll-ui after framework detection.
  Do not auto-trigger — invoked by mobiscroll-ui only.
---

# Mobiscroll — JavaScript Patterns

> This skill is loaded by `mobiscroll-ui`. If you arrived here directly, invoke
> `mobiscroll-ui` first to run environment detection and the mandatory schema lookup.

Package: `@mobiscroll/javascript`
CSS import: `import '@mobiscroll/javascript/dist/css/mobiscroll.min.css'`

## Initialization Pattern

```javascript
import * as mobiscroll from '@mobiscroll/javascript';

var inst = mobiscroll.eventcalendar('#my-calendar', {
  data: events,
  view: { scheduler: { type: 'week' } },
  onEventCreate: function (args) {
    // handle
  }
});
```

Component factory functions are lowercase: `mobiscroll.eventcalendar()`,
`mobiscroll.datepicker()`, `mobiscroll.select()`, `mobiscroll.popup()`.

## Key Conventions

- Initialization targets a DOM element (CSS selector string or HTMLElement reference).
- All configuration passes as an options object (second argument).
- Event handlers are properties in the options object: `onEventCreate`, `onCellClick`.
- The factory function returns an instance with methods: `inst.setOptions()`,
  `inst.getEvents()`, `inst.destroy()`.
- No JSX, no templates, no decorators — pure DOM + callbacks.

## Anti-Patterns

| WRONG | RIGHT |
|:---|:---|
| `import { Eventcalendar } from '@mobiscroll/react'` | `import * as mobiscroll from '@mobiscroll/javascript'` |
| `<Eventcalendar />` JSX component | `mobiscroll.eventcalendar('#el', { ... })` |
| React hooks (`useState`, `useEffect`) | Vanilla DOM events and callbacks |
| Angular `@Component` decorators | Imperative initialization on DOM elements |
| Vue `<script setup>` or `ref()` | Plain JS variables and event handlers |
| jQuery `$('#el').mobiscroll()` plugin pattern | `mobiscroll.eventcalendar('#el', { ... })` |
| `@mobiscroll/react`, `@mobiscroll/angular`, etc. | `@mobiscroll/javascript` only |

## Event Handling

```javascript
mobiscroll.eventcalendar('#el', {
  onEventClick: function (args) {
    console.log(args.event);    // the clicked event object
    console.log(args.domEvent); // the original DOM event
  }
});
```

## Dynamic Updates

```javascript
var inst = mobiscroll.eventcalendar('#el', { ... });
inst.setOptions({ data: newEvents });
```

## Custom Rendering

```javascript
// v6: renderSchedulerEvent (v5: renderScheduleEvent — deprecated in v6)
mobiscroll.eventcalendar('#el', {
  renderSchedulerEvent: function (data) {
    return '<div class="custom-event">' + data.title + '</div>';
  }
});
```

> **v5 note:** `renderScheduleEvent` (without `r`) was the correct name in v5. In v6 it is
> deprecated — use `renderSchedulerEvent` instead. The same rename applies to all
> `*Content` variants.

Always verify available render options via `mcp__mobiscroll__getComponentSchema`.
