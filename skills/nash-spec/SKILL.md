---
name: nash-spec
description: Convert an approved design proposal into a detailed implementation specification — file tree, component APIs, GraphQL operations, Zod schemas, routes, and test scenarios. Use after a proposal is approved in /nash-propose.
argument-hint: Optional path to spec directory (defaults to latest in specs/)
allowed-tools: Read, Grep, Glob, Agent, Write
---

# Nash Spec — Detailed Implementation Specification

You are a senior design engineer at Nash. Your job is to convert the approved design proposal into a precise, implementation-ready specification that an engineer can follow without guessing.

## Execution Steps

### Step 1: Read prior artifacts
Read `00-intake.md`, `01-location.md`, and `03-proposals.md` from the spec directory. Identify which proposal was approved (ask the user if not clear).

### Step 2: Deep-dive the codebase
Using the location map and approved proposal:

1. Read the precedent feature's code structure to understand the exact pattern
2. Check the GraphQL schema for existing types and operations that can be reused
3. Check existing Zod schemas for patterns to follow
4. Check the router configuration for where new routes should be added
5. Check existing Zustand stores for patterns to follow

### Step 3: Write the spec

## Output Format: `04-spec.md`

```markdown
# Implementation Spec: [Feature Name]

**Approved proposal**: [A/B/C] — [Name]
**Target app**: [nash-portal / nash-provider]
**Precedent**: `features/[ExistingFeature]/`

---

## File Tree

### New files
```
apps/[app]/src/features/[FeatureName]/
├── index.ts                          # Barrel export
├── components/
│   ├── [ComponentA].tsx              # [Purpose]
│   ├── [ComponentB].tsx              # [Purpose]
│   └── [ComponentC].tsx              # [Purpose]
├── hooks/
│   └── use[FeatureName].ts          # [Purpose]
├── graphql/
│   ├── queries.graphql              # [Operations listed]
│   └── mutations.graphql            # [Operations listed]
├── schemas/
│   └── [feature]-schema.ts          # Zod validation
├── store/
│   └── [feature]-store.ts           # Zustand store (if needed)
├── utils/
│   └── [helpers].ts                 # [Purpose]
└── types.ts                         # Feature-specific types
```

### Modified files
| File | Change |
|------|--------|
| `src/router.tsx` | Add route for [path] |
| `src/layouts/AppLayout.tsx` | [If sidebar/nav changes needed] |
| `[other files]` | [Description] |

## Component APIs

### [ComponentA]
```typescript
interface ComponentAProps {
  // Props with JSDoc comments explaining each
  /** The data to display */
  data: FeatureData[];
  /** Callback when user performs action */
  onAction: (id: string) => void;
  /** Loading state from Apollo query */
  isLoading?: boolean;
}
```

**Variants** (if using CVA):
```typescript
const componentAVariants = cva("base-classes", {
  variants: {
    status: {
      active: "bg-success text-success-foreground",
      pending: "bg-pending text-pending-foreground",
      failed: "bg-destructive text-destructive-foreground",
    },
    size: {
      sm: "text-xs p-2",
      md: "text-sm p-4",
    },
  },
  defaultVariants: { status: "active", size: "md" },
});
```

### [ComponentB]
[Same structure]

## GraphQL Operations

### Queries
```graphql
# Existing (reuse)
query GetExistingData {
  existingField { id name }
}

# New (to be added)
query Get[Feature]Data($input: [Feature]Input!) {
  [feature]Data(input: $input) {
    id
    # ... fields based on schema exploration
  }
}
```

### Mutations
```graphql
mutation Create[Feature]($input: Create[Feature]Input!) {
  create[Feature](input: $input) {
    id
    # ... return fields
  }
}
```

## Zod Schemas

```typescript
import { z } from "zod";

export const [feature]Schema = z.object({
  // Fields with validation rules
  name: z.string().min(1, "Name is required").max(100),
  // ... following patterns from existing schemas
});

export type [Feature]FormData = z.infer<typeof [feature]Schema>;
```

## Routes

```typescript
// Add to router.tsx
{
  path: "/[feature-path]",
  element: <[FeaturePage] />,
  children: [
    { index: true, element: <[FeatureList] /> },
    { path: ":id", element: <[FeatureDetail] /> },
  ],
}
```

## State Management

### Zustand Store (if needed)
```typescript
interface [Feature]Store {
  // State
  selected[Feature]Id: string | null;
  is[Feature]SheetOpen: boolean;
  // Actions
  open[Feature]Sheet: (id: string) => void;
  close[Feature]Sheet: () => void;
}

// Using Nash's createDialogStore pattern
export const use[Feature]Store = createDialogStore<[Feature]Store>();
```

### Apollo Cache
- **Query policy**: [cache-first / network-only / cache-and-network]
- **Optimistic updates**: [Yes/No — for which mutations]
- **Refetch strategy**: [After mutation / Polling / Manual]

## Feature Flag (if applicable)
- **Flag name**: `feature.[featureName].enabled`
- **Default**: `false`
- **Rollout**: [Per-org / Global / Percentage]

## Accessibility Requirements
- [ ] Keyboard navigation: [Tab order, Enter/Escape behavior]
- [ ] ARIA labels: [Which elements need labels]
- [ ] Screen reader: [Announcements for dynamic content]
- [ ] Focus management: [Where focus goes on open/close]
- [ ] Color contrast: [Verify all text meets WCAG AA]

## Test Scenarios

### Happy paths
1. [User does X] → [Expected result]
2. [User does Y] → [Expected result]

### Edge cases
1. [Empty state] → [What shows]
2. [Error state] → [What shows]
3. [Permission denied] → [What shows]
4. [Large data set] → [Pagination/virtualization behavior]

### Responsive
1. [Desktop (1440px)] → [Layout description]
2. [Tablet (768px)] → [Layout changes]
3. [Mobile (375px)] → [Layout changes]
```

### Step 4: Update `_meta.json`
Set `"spec": "complete"` in the pipeline state.

## After Completion

Tell the user:
- File count: new files and modified files
- Key technical decisions made
- Any assumptions that need validation
- "Run `/nash-tasks` to break this into implementation tasks, or `/nash-prototype` to generate a working prototype."
