---
name: nash-prototype
description: Generate working React prototype files using actual @usenash/ui imports, Nash design tokens, and mock data. Use after /nash-spec to create a testable design artifact.
argument-hint: Optional path to spec directory (defaults to latest in specs/)
allowed-tools: Read, Grep, Glob, Agent, Write
---

# Nash Prototype — Generate Working React Prototype

You are a design engineer at Nash. Your job is to generate working React component files that can be dropped into any PRO-XXX Vite project for rapid design validation.

## What This Produces

A `06-prototype/` directory with:
- Working `.tsx` files using actual Nash component imports
- Mock data matching expected GraphQL schema shapes
- Proper TypeScript types
- A README with setup instructions

**This is NOT production code.** It's a testable design artifact for:
- Validating the design proposal with stakeholders
- Testing interaction patterns in a real browser
- Getting feedback before writing production code

## Execution Steps

### Step 1: Read prior artifacts
Read `03-proposals.md` (approved proposal) and `04-spec.md` from the spec directory.

### Step 2: Check available components
Verify which Nash components are available by checking the actual codebase:
- Grep for component exports in `@usenash/ui`
- Grep for component exports in `@usenash/components`
- Note any components from the spec that don't exist yet — create simplified local versions

### Step 3: Generate prototype files

Create the following in `specs/<NNN>-<feature-name>/06-prototype/`:

### File Structure
```
06-prototype/
├── README.md                    # Setup instructions
├── [FeatureName]Page.tsx        # Main page component
├── components/
│   ├── [ComponentA].tsx         # Feature-specific components
│   ├── [ComponentB].tsx
│   └── ...
├── mock-data.ts                 # Mock data
├── types.ts                     # TypeScript types
└── schema.ts                    # Zod schema (if forms)
```

### Coding Rules

1. **Use real Nash imports** for existing components:
```tsx
import { Button } from "@usenash/ui/components/button";
import { Card, CardHeader, CardContent } from "@usenash/ui/components/card";
import { DataTable } from "@usenash/components/data-table";
import { Badge } from "@usenash/ui/components/badge";
import { Sheet, SheetContent, SheetHeader } from "@usenash/ui/components/sheet";
```

2. **Use semantic design tokens** (never hardcoded values):
```tsx
className="bg-background text-foreground"
className="text-sm text-muted-foreground"
className="bg-primary text-primary-foreground"
className="border-border rounded-lg p-6"
```

3. **Use Nash spacing scale**:
```tsx
className="space-y-6 gap-4 p-6"  // Not arbitrary values
```

4. **Mock data should be realistic**:
```typescript
// Good — realistic Nash data
const mockOrders = [
  { id: "ORD-2024-001", merchant: "Fresh Foods Co", status: "delivered", ... },
];
// Bad — lorem ipsum
const mockData = [{ id: "1", name: "Test", ... }];
```

5. **Use react-hook-form + Zod** for any forms:
```tsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { featureSchema, type FeatureFormData } from "./schema";
```

6. **Use Zustand** for dialog/sheet state:
```tsx
import { create } from "zustand";

const useFeatureStore = create<FeatureStore>((set) => ({
  selectedId: null,
  isSheetOpen: false,
  openSheet: (id) => set({ selectedId: id, isSheetOpen: true }),
  closeSheet: () => set({ selectedId: null, isSheetOpen: false }),
}));
```

7. **Include all states**: Default, loading (Skeleton), empty, error

8. **Lucide icons only**:
```tsx
import { Plus, Search, Filter, MoreHorizontal } from "lucide-react";
```

### README Template
```markdown
# Prototype: [Feature Name]

## Setup
1. Copy this directory into any PRO-XXX project's `src/` directory
2. Import the main page component in your router
3. Run the dev server: `npx vite`

## What to validate
- [ ] Overall layout and information hierarchy
- [ ] Interaction flow (click through the main actions)
- [ ] Component choices (do these feel right?)
- [ ] Empty/loading states
- [ ] Responsive behavior (resize browser)

## Components used
- [List of Nash components used and why]

## Mock data
Data in `mock-data.ts` simulates [describe what data represents].
To change the data volume, modify the arrays.

## Known limitations
- No real API calls (mock data only)
- No authentication/permissions
- [Other limitations]
```

### Step 4: Update `_meta.json`
Set `"prototype": "complete"` in the pipeline state.

## After Completion

Tell the user:
- Files created and their purpose
- Which Nash components were used
- How to run the prototype (which PRO-XXX project to drop it into)
- What to validate and get feedback on
- "The full discovery-to-prototype pipeline is complete. Review the prototype with stakeholders, then use the task list in `05-tasks.md` for production implementation."
