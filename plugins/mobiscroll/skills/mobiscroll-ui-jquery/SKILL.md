---
name: mobiscroll-ui-jquery
description: |
  Mobiscroll jQuery patterns — .mobiscroll() plugin chain, event callbacks, dynamic
  updates, @mobiscroll/jquery imports. Invoked by mobiscroll-ui after framework detection.
  Do not auto-trigger — invoked by mobiscroll-ui only.
---

# Mobiscroll — jQuery Patterns

> This skill is loaded by `mobiscroll-ui`. If you arrived here directly, invoke
> `mobiscroll-ui` first to run environment detection and the mandatory schema lookup.

Package: `@mobiscroll/jquery`
CSS import: `import '@mobiscroll/jquery/dist/css/mobiscroll.min.css'`
Requires jQuery loaded before Mobiscroll.

## Initialization Pattern

```javascript
import $ from 'jquery';
import '@mobiscroll/jquery';

$('#my-calendar').mobiscroll().eventcalendar({
  data: events,
  view: { scheduler: { type: 'week' } },
  onEventCreate: function (args) {
    // handle
  }
});
```

## Key Conventions

- Initialization chains from a jQuery selector: `$('#el').mobiscroll().eventcalendar({...})`.
- The `.mobiscroll()` bridge call is required before the component method.
- Component methods are lowercase: `.eventcalendar()`, `.datepicker()`, `.select()`, `.popup()`.
- Event handlers are properties in the options object (same as vanilla JS).
- Instance access: `var inst = $('#el').mobiscroll().eventcalendar({...});` returns the
  Mobiscroll instance (not the jQuery object).

## Anti-Patterns

| WRONG | RIGHT |
|:---|:---|
| `$('#el').eventcalendar({...})` without `.mobiscroll()` | `$('#el').mobiscroll().eventcalendar({...})` |
| `mobiscroll.eventcalendar('#el', {...})` vanilla JS pattern | `$('#el').mobiscroll().eventcalendar({...})` jQuery pattern |
| `import { Eventcalendar } from '@mobiscroll/react'` | `import '@mobiscroll/jquery'` |
| React JSX or Angular templates | jQuery selector + plugin chain |
| `@mobiscroll/javascript` or `@mobiscroll/react` | `@mobiscroll/jquery` only |

## Event Handling

```javascript
$('#el').mobiscroll().eventcalendar({
  onEventClick: function (args) {
    console.log(args.event);
  }
});
```

## Dynamic Updates

```javascript
var inst = $('#el').mobiscroll().eventcalendar({...});
inst.setOptions({ data: newEvents });
```

## Custom Rendering

```javascript
// v6: renderSchedulerEvent (v5: renderScheduleEvent — deprecated in v6)
$('#el').mobiscroll().eventcalendar({
  renderSchedulerEvent: function (data) {
    return '<div class="custom-event">' + data.title + '</div>';
  }
});
```

> **v5 note:** `renderScheduleEvent` (without `r`) was the correct name in v5. In v6 it is
> deprecated — use `renderSchedulerEvent` instead. The same rename applies to all
> `*Content` variants.

Always verify available render options via the `getComponentSchema` MCP tool before writing
code. If the MCP tools are unavailable, fall back to the jQuery docs (see `mobiscroll-ui`
→ Documentation Sources), then to general knowledge — and say so.
