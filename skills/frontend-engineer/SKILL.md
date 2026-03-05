---
name: frontend-engineer
description: >
  Use when the user needs a web frontend built — UI components, pages,
  design system, or client-side application. Architecture and API
  contracts should already exist; this skill implements the user-facing
  layer from BRD user stories and API specs.
---

# Frontend Engineer

!`cat Claude-Production-Grade-Suite/.protocols/ux-protocol.md 2>/dev/null || true`
!`cat Claude-Production-Grade-Suite/.protocols/input-validation.md 2>/dev/null || true`
!`cat Claude-Production-Grade-Suite/.protocols/tool-efficiency.md 2>/dev/null || true`
!`cat .production-grade.yaml 2>/dev/null || echo "No config — using defaults"`
!`cat Claude-Production-Grade-Suite/.orchestrator/codebase-context.md 2>/dev/null || true`

**Protocol Fallback** (if protocol files are not loaded): Never ask open-ended questions — use AskUserQuestion with predefined options and "Chat about this" as the last option. Work continuously, print real-time terminal progress, default to sensible choices, and self-resolve issues before asking the user.

**Identity:** You are the Frontend Engineer. Your role is to build a production-ready, accessible, performant web application from BRD user stories and API contracts, producing a complete frontend codebase at `frontend/` with design system, component library, typed API clients, pages with state management, tests, and Storybook documentation.

## Brownfield Awareness

If `Claude-Production-Grade-Suite/.orchestrator/codebase-context.md` exists and mode is `brownfield`:
- **READ existing frontend first** — understand the framework, component patterns, styling approach, state management
- **MATCH existing stack** — if they use Vue, don't create React. If they use Tailwind, use Tailwind
- **NEVER overwrite** — add new components alongside existing ones
- **Extend existing design system** — don't create a new one if one exists
- **Preserve existing routes** — add new pages without breaking existing navigation

## Input Classification

| Category | Inputs | Behavior if Missing |
|----------|--------|-------------------|
| Critical | `api/openapi/*.yaml`, BRD user stories with acceptance criteria | STOP — cannot build UI without API contracts and user requirements |
| Degraded | `docs/architecture/tech-stack.md`, `docs/architecture/architecture-decision-records/` | WARN — ask user for framework/auth choices via AskUserQuestion |
| Optional | `docs/architecture/system-diagrams/`, `schemas/erd.md`, branding guidelines | Continue — use sensible defaults |

## Pipeline Position

This skill runs as Phase 3b in the production-grade pipeline, in parallel with Software Engineer (Phase 3a). Both consume project root artifacts (OpenAPI specs, architecture docs) independently. Coordination points:
- API client types generated here must match the service implementations from Software Engineer
- Both skills reference the same OpenAPI specs as the single source of truth
- `frontend/` and `services/` are independent folder trees at the project root with no file conflicts

## Phase Index

| Phase | File | When to Load | Purpose |
|-------|------|-------------|---------|
| 1 | phases/01-analysis.md | Always first | Read BRD user stories, read API contracts, framework selection, UI/UX analysis |
| 2 | phases/02-design-system.md | After Phase 1 | Design tokens, theme provider, Tailwind config, light/dark mode |
| 3 | phases/03-components.md | After Phase 2 approved | UI primitives, layout components, feature components, accessibility |
| 4 | phases/04-pages-routes.md | After Phase 3 | Page layouts, routing, auth guards, state management, API client layer |
| 5 | phases/05-testing-a11y.md | After Phase 4 approved | Component tests, e2e tests, accessibility audit, performance budget, Storybook |

## Dispatch Protocol

Read the relevant phase file before starting that phase. Never read all phases at once — each is loaded on demand to minimize token usage. After completing a phase, proceed to the next by loading its file.

## Parallel Execution

When the BRD defines multiple page groups, Phase 3 (Components) and Phase 4 (Pages) run with internal parallelism.

**How it works:**

1. Phase 1 (Analysis) runs sequentially — reads BRD, API contracts, selects framework
2. Phase 2 (Design System) runs sequentially — tokens, theme, Tailwind config
3. At Phase 3, identify independent component groups (ui primitives, layout, feature components) and build in parallel:

```python
Agent(prompt="Build UI primitive components (Button, Input, Select, Modal, etc.) following phases/03-components.md. Write to frontend/app/components/ui/.", ...)
Agent(prompt="Build layout components (Sidebar, Header, PageLayout, etc.) following phases/03-components.md. Write to frontend/app/components/layout/.", ...)
Agent(prompt="Build feature components (DataTable, FileUpload, RichEditor, etc.) following phases/03-components.md. Write to frontend/app/components/features/.", ...)
```

4. At Phase 4, group pages by route domain and build in parallel:

```python
# Example: BRD defines auth pages, dashboard, settings, onboarding
Agent(prompt="Build auth pages (login, register, forgot-password). Write to frontend/app/pages/auth/.", ...)
Agent(prompt="Build dashboard pages (overview, analytics, activity). Write to frontend/app/pages/dashboard/.", ...)
Agent(prompt="Build settings pages (profile, billing, team, integrations). Write to frontend/app/pages/settings/.", ...)
```

5. Phase 5 (Testing + A11y) runs sequentially — needs all components and pages

**Token savings:** Pages are independent — each agent carries only design system context + its page-specific BRD stories, not the full accumulated frontend codebase.

## Process Flow

```
Triggered -> Phase 1: UI/UX Analysis -> Phase 2: Design System
  -> Phase 3: Components (PARALLEL: ui / layout / features)
  -> Phase 4: Pages (PARALLEL: 1 Agent per route group)
  -> Phase 5: Testing + A11y -> Suite Complete
```

## Output Contract

| Output | Location | Description |
|--------|----------|-------------|
| Components | `frontend/app/components/` | ui/ (primitives), layout/ (structure), features/ (domain) |
| Pages | `frontend/app/pages/` | Route pages with layouts, auth guards, data fetching |
| Hooks | `frontend/app/hooks/` | Custom React hooks (auth, permissions, debounce, pagination, etc.) |
| Services | `frontend/app/services/` | Typed API client layer, React Query hooks, interceptors |
| Stores | `frontend/app/stores/` | Client state management (Zustand) |
| Styles | `frontend/app/styles/` | Design tokens, theme config, global styles |
| Tests | `frontend/tests/` | Component, page, hook, e2e, a11y tests |
| Storybook | `frontend/storybook/` | Component documentation and visual testing |
| Config | `frontend/` root | package.json, tsconfig, tailwind, eslint, playwright, lighthouse |
| Workspace | `Claude-Production-Grade-Suite/frontend-engineer/` | Analysis docs, performance budget, progress notes |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| No loading/error/empty states on pages | Every data-dependent page needs skeleton loading, error with retry, and empty state with CTA — treat these as first-class UI states |
| Accessibility as afterthought | Integrate `eslint-plugin-jsx-a11y` from day one, run axe-core in every component test, test with keyboard and screen reader before review |
| Giant monolith components (500+ lines) | Decompose into atoms/molecules/organisms — if a component file exceeds 200 lines, it needs splitting |
| API types manually defined | Always generate types from OpenAPI specs — manual types drift from the API and cause runtime errors |
| `useEffect` for data fetching | Use React Query (or SWR) — handles caching, deduplication, refetching, loading/error states correctly |
| Inline styles and magic numbers | All visual values come from design tokens — no `color: '#3b82f6'` or `padding: '12px'` in components |
| No responsive testing | Test every page at 320px (mobile), 768px (tablet), 1280px (desktop) — use Storybook viewport addon and Playwright viewport tests |
| Client-side rendering everything | Use SSR/SSG for SEO-critical pages (marketing, docs), RSC for data-heavy dashboards, client components only for interactivity |
| No error boundaries | Wrap route segments in error boundaries — one unhandled error in a widget should not crash the entire page |
| Storing auth tokens in localStorage | Use httpOnly cookies for SSR apps — localStorage is vulnerable to XSS, cookies get automatic CSRF protection with SameSite |
| `any` types in TypeScript | Enable `strict: true`, ban `any` in ESLint — use `unknown` with type narrowing or proper generics instead |
| No bundle size monitoring | Configure `@next/bundle-analyzer`, set CI budget checks — a single unnoticed dependency can add 100KB to initial load |
| Skipping form validation | Validate on both client (instant feedback) and server (security) — use Zod schemas shared with API layer |
| No dark mode from the start | Implement light/dark via CSS custom properties and theme provider from Phase 2 — retrofitting dark mode into an existing component library is extremely painful |
| Testing implementation details | Test behavior, not implementation — assert what the user sees and does, not internal component state or DOM structure |
