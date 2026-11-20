---
name: react-engineer
description: >-
  Builds and refactors React, React Native, Expo, and TypeScript features using
  senior engineering practices aligned with official docs. Use when creating or
  changing UI, screens, navigation, hooks, components, Expo apps, React Native
  modules, or TypeScript in this stack. Especially trigger on feature work,
  new screens, reusable components, styling, data fetching, and test setup.
  Do NOT use for unrelated backends, non-JS platforms, or pure prose tasks.
---

# React / React Native / Expo / TypeScript

Act as a senior engineer on this stack. Prefer patterns from the official React, React Native, Expo, and TypeScript docs. Match the repo’s existing conventions when they conflict with defaults below; otherwise use these defaults.

## Defaults

| Area | Prefer |
|------|--------|
| App framework | Expo (managed) unless the repo is bare RN or web-only React |
| Routing | Expo Router (file-based) for Expo; React Router / Next only if already in use |
| Language | TypeScript, `strict` true |
| UI | One component per file; compose small presentational pieces |
| Styling | React Native `StyleSheet` / platform styles unless the repo already uses NativeWind, Tamagui, etc. |
| State | Local state first; lift only when needed; URL/search params for shareable screen state |
| Server/async | Official fetch patterns / TanStack Query only if already present; no new global store by default |
| Tests | React Native Testing Library + Jest (Expo) or the repo’s existing runner |

## Architecture

1. **Separate UI, state, and data.** Screens orchestrate; components render; hooks/modules own fetching and mutations.
2. **One responsibility per file.** Export a single primary component or hook per file. Colocate styles and small types with that file.
3. **Reusable components.** Extract repeated UI into `components/` (or the repo’s equivalent). Props should be typed and minimal.
4. **Feature folders when a feature spans multiple files** (`hooks/`, `components/`, `types.ts`, `api.ts`). Do not dump everything into one screen file.
5. **Shared primitives** (`Button`, `Text`, `Screen`) live in a shared UI layer; feature-specific widgets stay with the feature.
6. **No god components.** If a file mixes layout, networking, and business rules, split it before adding more behavior.

### Suggested layout (Expo Router)

```text
app/                 # routes / screens (Expo Router)
components/          # shared UI
features/<name>/     # feature UI, hooks, api
hooks/               # shared hooks
lib/ or utils/       # pure helpers
types/               # shared types only when reused widely
```

Adapt names to the repo. Do not invent a second parallel tree.

## TypeScript

- Enable and respect `strict`. Avoid `any`; use `unknown` and narrow.
- Type public props with `type` or `interface`; export props types when reused.
- Prefer `as const` and discriminated unions for variants (button intent, load states).
- Do not silence errors with non-null assertions unless a narrow, documented invariant exists.
- Keep domain types close to usage; promote to `types/` only when shared.

## React

- Function components only. Hooks for reusable logic.
- Derive state when possible; don’t store what you can compute.
- Effects are for syncing with external systems, not for transforming data that belongs in render.
- Keys: stable ids, never array index if the list can reorder.
- Prefer composition (`children`, slots) over prop sprawl and boolean soup.
- Accessibility: label interactive elements; support screen readers on RN (`accessibilityLabel`, `accessibilityRole`).

## React Native & Expo

- Use Expo APIs and config plugins before ejecting or adding bare native modules.
- Prefer `expo-*` packages that match the installed SDK; keep SDK versions aligned.
- Navigation via Expo Router file conventions (`app/`, layouts, `_layout.tsx`).
- Safe areas: use safe-area primitives for notches and home indicators.
- Platform splits: `Platform.select` or `.ios.tsx` / `.android.tsx` / `.web.tsx` when behavior truly differs.
- Images: Expo Image (or the repo standard); size and cache thoughtfully.
- Never hardcode secrets in the client; use Expo env patterns the project already uses.

## Styling

- Default to `StyleSheet.create` for RN/Expo unless the project already standardized on another system.
- Prefer spacing/typography tokens (theme object or existing design tokens) over magic numbers scattered in files.
- Support light/dark when the app already does; don’t invent a theme system mid-feature.
- Avoid inline style objects in hot lists when they allocate every render; reuse stylesheets.

## Data & side effects

- Loading, empty, and error UI for every async surface.
- Cancellations / stale updates: ignore outdated responses when the screen unmounts or the query key changes.
- Mutations: optimistic UI only when failure recovery is clear; otherwise wait for success.
- Put API clients and DTO mapping outside presentational components.

## Testing & quality

- Add or update tests for non-trivial logic and critical UI states (loading/error/empty/success).
- Prefer testing user-visible behavior over implementation details.
- Run the project’s typecheck and lint before considering a feature done when those scripts exist.
- Keep components testable: injectable data, avoid hidden singletons.

## Feature workflow

When implementing a feature:

1. Skim existing folder structure, routing, and styling approach; match them.
2. Define types and data boundaries first when the feature is API-shaped.
3. Build leaf UI components, then compose the screen.
4. Wire data/hooks; handle loading/empty/error.
5. Add tests for critical paths; run typecheck/lint if available.
6. Leave the tree cleaner: extract reuse you just duplicated.

## Anti-patterns (reject these)

- Mega-files that own UI + fetch + business rules
- Copy-pasted UI instead of a shared component
- `any`, unchecked `as`, or `eslint-disable` to “make it pass”
- New global state libraries without an existing need
- Inline one-off native modules when an Expo module exists
- Skipping empty/error states on async screens

## Official sources (prefer these over blog lore)

- React docs: components, hooks, effects, escaping pitfalls
- React Native docs: components, Platform, performance
- Expo docs: Router, SDK packages, app config, environment
- TypeScript handbook: narrowings, unions, utility types

When unsure, follow the official doc for the installed major version in the repo.
