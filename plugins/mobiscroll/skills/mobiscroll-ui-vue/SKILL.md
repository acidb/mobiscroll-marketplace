---
name: mobiscroll-ui-vue
description: |
  Mobiscroll Vue patterns — MbscEventcalendar components, Vue 3 Composition API, named
  slots, @mobiscroll/vue imports. Invoked by mobiscroll-ui after framework detection.
  Do not auto-trigger — invoked by mobiscroll-ui only.
---

# Mobiscroll — Vue Patterns

> This skill is loaded by `mobiscroll-ui`. If you arrived here directly, invoke
> `mobiscroll-ui` first to run environment detection and the mandatory schema lookup.

Package: `@mobiscroll/vue`
CSS import: `import '@mobiscroll/vue/dist/css/mobiscroll.min.css'`
**Vue 3 Composition API with `<script setup>` only** — never Vue 2 Options API or `Vue.component()`.

## Initialization Pattern

```vue
<script setup>
import { ref } from 'vue';
import { MbscEventcalendar } from '@mobiscroll/vue';

const events = ref([]);
const myView = { scheduler: { type: 'week' } };

function handleEventCreate(args) {
  // handle
}
</script>

<template>
  <MbscEventcalendar
    :data="events"
    :view="myView"
    @event-create="handleEventCreate"
  />
</template>
```

## Key Conventions

- Components are named exports with `Mbsc` prefix: `MbscEventcalendar`, `MbscDatepicker`,
  `MbscSelect`, `MbscPopup`.
- Prop binding uses `:prop` syntax: `:data="events"`, `:view="myView"`.
- Event binding uses `@event-name` syntax (kebab-case): `@event-create`, `@cell-click`,
  `@page-change`.
- State uses Vue reactivity: `ref()`, `reactive()`, `computed()`.
- TypeScript types are prefixed `Mbsc` (same as Angular): `MbscEventcalendarView`,
  `MbscCalendarEvent`.

## Anti-Patterns

| WRONG | RIGHT |
|:---|:---|
| Using Vue 2 Options API or `Vue.component()` | Use Vue 3 Composition API with `<script setup>` only |
| `<Eventcalendar />` without Mbsc prefix | `<MbscEventcalendar />` |
| `@onEventCreate` (React-style on-prefix in template) | `@event-create` (kebab-case, no on-prefix) |
| `import { Eventcalendar } from '@mobiscroll/react'` | `import { MbscEventcalendar } from '@mobiscroll/vue'` |
| `mobiscroll.eventcalendar('#el', {...})` imperative init | `<MbscEventcalendar :data="events" />` in template |
| Using `@mobiscroll/angular` or `@mobiscroll/javascript` | `@mobiscroll/vue` only |
| Using React `useState` / `useEffect` in Vue code | Vue 3 uses `ref()`, `computed()`, `onMounted()` etc. |

## Event Handling

```vue
<MbscEventcalendar @event-click="onEventClick" />
```

```typescript
function onEventClick(args) {
  console.log(args.event);  // the clicked event object
}
```

## Slot-Based Customization

```vue
<!-- v6: #schedulerEvent (v5: #scheduleEvent — deprecated in v6) -->
<MbscEventcalendar :data="events">
  <template #schedulerEvent="{ data }">
    <div class="custom-event">{{ data.title }}</div>
  </template>
</MbscEventcalendar>
```

> **v5 note:** `scheduleEvent` (without `r`) was the correct slot name in v5. In v6 it is
> deprecated — use `schedulerEvent` instead. The same rename applies to all `*Content`
> variants.

Always verify available slots via the `getComponentSchema` MCP tool (request only the entities you need by `names` after reviewing the index) before writing code. If
the MCP tools are unavailable, fall back to the Vue docs (see `mobiscroll-ui` →
Documentation Sources), then to general knowledge — and say so.
