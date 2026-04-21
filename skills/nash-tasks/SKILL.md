---
name: nash-tasks
description: Break an implementation spec into dependency-ordered, Linear-ready tasks with effort estimates. Use after /nash-spec to create a development plan.
argument-hint: Optional path to spec directory (defaults to latest in specs/)
allowed-tools: Read, Write
---

# Nash Tasks — Dependency-Ordered Task Breakdown

You are a technical project manager for Nash frontend. Your job is to break the implementation spec into atomic, dependency-ordered tasks that are ready to be copied into Linear.

## Execution Steps

### Step 1: Read prior artifacts
Read `04-spec.md` from the spec directory. Also reference `01-location.md` for context on affected modules.

### Step 2: Decompose into tasks

Break the spec into tasks following these rules:

1. **Atomic**: Each task is completable in one focused session (max ~4 hours)
2. **Dependency-ordered**: Tasks list their prerequisites
3. **File-specific**: Each task references exact files to create or modify
4. **Phased**: Group tasks into logical phases
5. **Parallel-aware**: Mark tasks that can be done simultaneously

### Task Phases (in order)

1. **Setup** — Branch, scaffolding, config changes, route registration
2. **Data Layer** — GraphQL operations, types, Zod schemas, stores
3. **Components** — UI components, from atomic to composed
4. **Integration** — Wiring components to data, navigation, state
5. **Polish** — Loading states, empty states, error handling, animations, a11y
6. **Verification** — Manual testing, edge cases, responsive check

### Effort Scale
- `[XS]` — < 30 min (config change, type addition)
- `[S]` — 30 min - 2 hours (single component, simple hook)
- `[M]` — 2-4 hours (complex component, full CRUD)
- `[L]` — 4-8 hours (multi-component integration, complex state)

## Output Format: `05-tasks.md`

```markdown
# Implementation Tasks: [Feature Name]

**Total tasks**: [N]
**Estimated effort**: [Sum of task sizes]
**Parallel tracks**: [N tasks can be parallelized]

---

## Phase 1: Setup `[XS-S total]`

- [ ] T001 `[XS]` Create feature directory scaffold — `features/[Name]/`
  - Create: index.ts, types.ts, directory structure
  - Depends on: nothing
  - Parallel: yes

- [ ] T002 `[XS]` Register route — `src/router.tsx`
  - Add route configuration for /[path]
  - Depends on: T001
  - Parallel: no

## Phase 2: Data Layer `[S-M total]`

- [ ] T003 `[S]` Define GraphQL operations — `features/[Name]/graphql/`
  - Create: queries.graphql, mutations.graphql
  - Run codegen after
  - Depends on: T001
  - Parallel: yes (with T004)

- [ ] T004 `[S]` Create Zod schemas — `features/[Name]/schemas/`
  - Create: [feature]-schema.ts
  - Depends on: T001
  - Parallel: yes (with T003)

- [ ] T005 `[S]` Create Zustand store — `features/[Name]/store/`
  - Create: [feature]-store.ts using createDialogStore
  - Depends on: T001
  - Parallel: yes (with T003, T004)

## Phase 3: Components `[M-L total]`

- [ ] T006 `[S]` Build [AtomicComponent] — `features/[Name]/components/[Component].tsx`
  - Props: [list key props]
  - Uses: [Nash components]
  - Depends on: T004 (for types)
  - Parallel: yes (with other component tasks)

- [ ] T007 `[M]` Build [ComposedComponent] — `features/[Name]/components/[Component].tsx`
  - Composes: T006's component + [others]
  - Depends on: T006
  - Parallel: no

## Phase 4: Integration `[M total]`

- [ ] T008 `[M]` Wire [PageComponent] — `features/[Name]/[Page].tsx`
  - Connect Apollo queries to components
  - Connect Zustand store to sheets/dialogs
  - Connect form submission to mutations
  - Depends on: T003, T005, T007
  - Parallel: no

## Phase 5: Polish `[S-M total]`

- [ ] T009 `[S]` Add loading & empty states — `features/[Name]/components/`
  - Skeleton loaders for initial load
  - Empty state illustration + CTA
  - Depends on: T008
  - Parallel: yes (with T010)

- [ ] T010 `[S]` Add error handling — `features/[Name]/components/`
  - Apollo error states
  - Form validation error display
  - Toast notifications for mutations
  - Depends on: T008
  - Parallel: yes (with T009)

- [ ] T011 `[S]` Accessibility pass
  - Keyboard navigation
  - ARIA labels
  - Focus management
  - Depends on: T008
  - Parallel: yes

## Phase 6: Verification `[S total]`

- [ ] T012 `[S]` Manual testing — all happy paths
  - Test scenarios from spec
  - Depends on: T008, T009, T010
  - Parallel: no

- [ ] T013 `[XS]` Responsive check — 1440px, 768px, 375px
  - Depends on: T008
  - Parallel: yes (with T012)

---

## Dependency Graph

```
T001 ──┬── T002
       ├── T003 ──┐
       ├── T004 ──┤
       └── T005 ──┼── T006 ── T007 ── T008 ──┬── T009
                  │                           ├── T010
                  │                           ├── T011
                  │                           └── T012
                  └── T013
```

## Linear-Ready Format

Copy-paste each task as a Linear issue with:
- **Title**: T001: [Description]
- **Label**: [Feature Name]
- **Estimate**: [XS=0.5, S=1, M=3, L=5 points]
- **Blocked by**: [T-numbers]
```

### Step 3: Update `_meta.json`
Set `"tasks": "complete"` in the pipeline state.

## After Completion

Tell the user:
- Total task count and effort estimate
- How many tasks can be parallelized
- Critical path (longest dependency chain)
- "Tasks are ready to copy into Linear. Run `/nash-prototype` to generate a working prototype."
