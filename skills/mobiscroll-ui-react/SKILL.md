---
name: mobiscroll-ui-react
description: |
  Mobiscroll React patterns — JSX components, render props, hooks, @mobiscroll/react
  imports. Invoked by mobiscroll-ui after framework detection.
  Do not auto-trigger — invoked by mobiscroll-ui only.
---

# Mobiscroll — React Patterns

> This skill is loaded by `mobiscroll-ui`. If you arrived here directly, invoke
> `mobiscroll-ui` first to run environment detection and the mandatory schema lookup.

Package: `@mobiscroll/react`
CSS import: `import '@mobiscroll/react/dist/css/mobiscroll.min.css'`

## Initialization Pattern

React uses JSX components with PascalCase names:

```jsx
import { useState, useMemo } from 'react';
import { Eventcalendar } from '@mobiscroll/react';

function MyCalendar() {
  const [events, setEvents] = useState([]);
  const myView = useMemo(() => ({ scheduler: { type: 'week' } }), []);

  return (
    <Eventcalendar
      data={events}
      view={myView}
      onEventCreate={(args) => { /* handle */ }}
    />
  );
}
```

## Key Conventions

- Components are named exports: `Eventcalendar`, `Datepicker`, `Select`, `Popup`.
- Props use camelCase: `data`, `view`, `selectedDate`, `onEventCreate`.
- Event handlers are props prefixed with `on`: `onEventCreate`, `onCellClick`, `onPageChange`.
- The `view` object should be memoized (via `useMemo`) to avoid unnecessary re-renders.
- State management uses standard React hooks (`useState`, `useEffect`, `useMemo`, `useCallback`).
- Controlled components: use `selectedDate` + `onSelectedDateChange` for controlled date state.

## Anti-Patterns

| WRONG | RIGHT |
|:---|:---|
| `import * as mobiscroll from '@mobiscroll/react'` | `import { Eventcalendar } from '@mobiscroll/react'` |
| `mobiscroll.eventcalendar('#el', {...})` imperative init | `<Eventcalendar data={events} view={myView} />` |
| Inline `view` object: `view={{ scheduler: { type: 'week' } }}` | Memoized: `const v = useMemo(() => ({...}), [])` |
| Using Angular `@Input()` or `@Output()` decorators | Use JSX props and `on*` event handlers |
| `@mobiscroll/javascript` or `@mobiscroll/angular` imports | `@mobiscroll/react` only |

## Event Handling

React event handlers receive an `args` object with event-specific data and an `inst`
reference to the component instance:

```jsx
<Eventcalendar
  onEventClick={(args) => {
    console.log(args.event);    // the clicked event object
    console.log(args.domEvent); // the original DOM event
  }}
/>
```

## Rendering Customization

Use render props (functions returning JSX) for custom rendering:

```jsx
{/* v6: renderSchedulerEvent (v5: renderScheduleEvent — deprecated in v6) */}
<Eventcalendar
  renderSchedulerEvent={(data) => (
    <div className="custom-event">{data.title}</div>
  )}
/>
```

> **v5 note:** `renderScheduleEvent` (without `r`) was the correct name in v5. In v6 it is
> deprecated — use `renderSchedulerEvent` instead. The same rename applies to all
> `*Content` variants.

Always verify available render props via the `getComponentSchema` MCP tool (request only the entities you need by `names` after reviewing the index) before writing
code. If the MCP tools are unavailable, fall back to the React docs (see `mobiscroll-ui`
→ Documentation Sources), then to general knowledge — and say so.
