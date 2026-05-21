---
name: mobiscroll-ui-angular
description: |
  Mobiscroll Angular patterns — mbsc- directives, MbscModule, ng-template customization,
  @mobiscroll/angular imports. Invoked by mobiscroll-ui after framework detection.
  Do not auto-trigger — invoked by mobiscroll-ui only.
---

# Mobiscroll — Angular Patterns

> This skill is loaded by `mobiscroll-ui`. If you arrived here directly, invoke
> `mobiscroll-ui` first to run environment detection and the mandatory schema lookup.

Package: `@mobiscroll/angular`
CSS: Add to `angular.json` `styles` array — **NEVER** `import` in TypeScript or component files.

```json
// angular.json
"styles": [
  "node_modules/@mobiscroll/angular/dist/css/mobiscroll.min.css",
  "src/styles.scss"
]
```

Module import: `MbscModule` registered in your Angular module **or** standalone component imports (not both).

## Initialization Pattern

```typescript
// app.module.ts (NgModule app)
import { MbscModule } from '@mobiscroll/angular';

@NgModule({
  imports: [MbscModule]
})
export class AppModule {}
```

```typescript
// OR: standalone component
import { MbscModule } from '@mobiscroll/angular';
import { Component } from '@angular/core';

@Component({
  imports: [MbscModule],  // standalone — do NOT also add to @NgModule
  template: `...`
})
export class CalendarComponent {}
```

```html
<!-- component.html -->
<mbsc-eventcalendar
  [data]="events"
  [view]="myView"
  (onEventCreate)="handleEventCreate($event)"
></mbsc-eventcalendar>
```

```typescript
// component.ts
import { MbscEventcalendarView, MbscCalendarEvent } from '@mobiscroll/angular';

@Component({ ... })
export class CalendarComponent {
  events: MbscCalendarEvent[] = [];
  myView: MbscEventcalendarView = { scheduler: { type: 'week' } };

  handleEventCreate(args: any) { /* handle */ }
}
```

## Key Conventions

- Component selectors are prefixed `mbsc-`: `mbsc-eventcalendar`, `mbsc-datepicker`, `mbsc-select`, `mbsc-popup`.
- Property binding uses `[prop]` syntax: `[data]="events"`, `[view]="myView"`.
- Event binding uses `(onEventName)` syntax: `(onEventCreate)="handler($event)"`.
- TypeScript types are prefixed `Mbsc`: `MbscEventcalendarView`, `MbscCalendarEvent`.
- `MbscModule` goes in `@Component({ imports: [MbscModule] })` for standalone, or `@NgModule({ imports: [MbscModule] })` for NgModule apps — never both at once.

## Anti-Patterns

| WRONG | RIGHT |
|:---|:---|
| `<Eventcalendar />` React JSX | `<mbsc-eventcalendar>` Angular template |
| `import { Eventcalendar } from '@mobiscroll/react'` | `import { MbscModule } from '@mobiscroll/angular'` |
| `import '@mobiscroll/angular/dist/css/mobiscroll.min.css'` in TS | Use `angular.json` `styles` array |
| `@import '~@mobiscroll/angular/...'` in styles.scss | Use `angular.json` `styles` array |
| Adding `MbscModule` to both `@Component` and `@NgModule` | Standalone: `@Component({ imports: [MbscModule] })`. NgModule: `@NgModule({ imports: [MbscModule] })`. Not both. |
| `mobiscroll.eventcalendar('#el', {...})` imperative init | `<mbsc-eventcalendar [data]="events">` in template |
| `(click)="handler()"` for Mobiscroll events | `(onEventCreate)="handler($event)"` Mobiscroll events |
| Using `@mobiscroll/javascript` imports | `@mobiscroll/angular` only |

## Event Handling

```html
<mbsc-eventcalendar
  (onEventClick)="onEventClick($event)"
></mbsc-eventcalendar>
```

```typescript
onEventClick(args: any) {
  console.log(args.event);  // the clicked event object
}
```

## Template Customization

```html
<!-- v6: schedulerEventTemplate (v5: scheduleEventTemplate — deprecated in v6) -->
<mbsc-eventcalendar [schedulerEventTemplate]="customEvent">
  <ng-template #customEvent let-data>
    <div class="custom-event">{{ data.title }}</div>
  </ng-template>
</mbsc-eventcalendar>
```

> **v5 note:** `scheduleEventTemplate` (without `r`) was the correct name in v5. In v6 it is
> deprecated — use `schedulerEventTemplate` instead. The same rename applies to all
> `*Content` variants.

Always verify available template inputs via `mcp__mobiscroll__getComponentSchema`.
