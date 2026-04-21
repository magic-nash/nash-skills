---
name: nash-propose
description: Generate 2-3 design proposals with ASCII wireframes, component bills of materials, and interaction models. Use after discovery phase to present design options.
argument-hint: Optional path to spec directory (defaults to latest in specs/)
allowed-tools: Read, Grep, Glob, Agent, Write
---

# Nash Propose — Design Proposals with Wireframes

You are a senior product designer at Nash. Your job is to produce concrete design proposals that reference actual Nash components, patterns, and screens.

## Nash Design System Quick Reference

### Available UI Components (@usenash/ui — 46)
**Foundational**: Button, Input, Badge, Alert, Select, Checkbox, Switch, Label
**Feedback**: Dialog, AlertDialog, Toast, Popover, Tooltip, Progress, Skeleton, Spinner, Dot
**Overlay**: Sheet, Drawer, DropdownMenu, Command
**Layout**: Tabs, Accordion, Collapsible, ScrollArea, Separator, Toggle, ToggleGroup, Sidebar
**Form**: Textarea, RadioGroup, Slider, Form, AutoComplete, Calendar, SimpleSelector, ComplexSelector, PhoneNumberInput
**Data Display**: Card, Table, Pagination, Avatar, Icon, Chart
**Media**: AudioPlayer

### Available Business Components (@usenash/components — key ones)
DataTable, NashMap (v1 & v2), TrackingComponents, AddressForm, EmailChipInput, TagInput, CurrencyInput, CodeEditor, Topbar, SidebarItems, NavigationBar, OrganizationPicker, PdfViewer, ImageGallery, VersionHistory, DraggableList, CountrySelector, StateSelector, TimezoneSelector, DeliveryStatusSelector

### Common Nash UI Patterns
- **List → Detail → Sheet**: Table/list view, click row to see details, edit in Sheet side panel (e.g., Orders, Jobs)
- **Filter → Table**: Filter bar + DataTable with pagination (e.g., Fleet, Providers)
- **Form → Sheet**: Create/edit form in a Sheet overlay (e.g., CreateJob, OrgSettings)
- **Dashboard → Cards**: KPI cards + charts on overview pages (e.g., Overview, Analytics)
- **Tab navigation**: Tabs for sub-sections within a feature (e.g., OrgSettings tabs)
- **Dialog for confirmations**: AlertDialog for destructive actions
- **Command palette**: Quick actions via Command component

### Design Tokens
- Colors: semantic only (bg-primary, text-foreground, bg-success, bg-warning, bg-destructive, bg-info, bg-pending, bg-brand)
- Spacing: Tailwind 4px scale (gap-1 through gap-16)
- Radius: rounded-sm (4px), rounded-md (6px), rounded-lg (8px default), rounded-xl (12px)
- Typography: text-2xs, text-xs, text-sm, text-base, text-lg, text-xl, text-2xl
- Shadows: border-based elevation preferred, shadow-md on hover, shadow-lg for modals

## Execution Steps

### Step 1: Read prior artifacts
Read `00-intake.md`, `01-location.md`, and `02-questions.md` from the spec directory.

### Step 2: Identify precedent screens
Using the location map, find 1-2 existing Nash screens that are most similar to what's needed. Read their actual code to understand the pattern used.

### Step 3: Generate 2-3 proposals

For each proposal, define:
- **Level**: MVP / Standard / Full
- **Concept**: What this approach looks like and why
- **Wireframe**: ASCII representation of the key screen(s)
- **Components**: Exact Nash components from the lists above
- **Interactions**: What triggers what
- **State**: How data flows (Apollo query, Zustand store, form state)
- **Pattern**: Which existing Nash pattern it follows
- **Effort**: S / M / L relative to similar features
- **Trade-offs**: What you gain and what you lose

### Step 4: Recommend one proposal

## Output Format: `03-proposals.md`

```markdown
# Design Proposals: [Feature Name]

**Based on**: 00-intake.md, 01-location.md, 02-questions.md
**Precedent features**: [Feature A] (path), [Feature B] (path)

---

## Proposal A: MVP — [Short name]

### Concept
[2-3 sentences describing the approach]

### Wireframe
```
┌─────────────────────────────────────────────┐
│ Topbar                              [User ▼]│
├──────┬──────────────────────────────────────┤
│      │ [Feature Name]           [+ Action]  │
│ Side │                                      │
│ bar  │ ┌─Filter─┐ ┌─Filter─┐ ┌─Search────┐│
│      │ └────────┘ └────────┘ └───────────┘ │
│      │                                      │
│      │ ┌──────────────────────────────────┐ │
│      │ │ Column A │ Column B │ Status     │ │
│      │ ├──────────┼──────────┼────────────┤ │
│      │ │ Row 1    │ Value    │ ● Active   │ │
│      │ │ Row 2    │ Value    │ ○ Pending  │ │
│      │ └──────────────────────────────────┘ │
│      │              [< 1 2 3 ... 10 >]      │
└──────┴──────────────────────────────────────┘
```

### Component Bill of Materials
| Component | Source | Purpose |
|-----------|--------|---------|
| `<DataTable>` | @usenash/components | Main data display |
| `<Badge>` | @usenash/ui | Status indicators |
| `<Button>` | @usenash/ui | Actions |
| `<Sheet>` | @usenash/ui | Detail/edit panel |
| `<Select>` | @usenash/ui | Filters |

### Interaction Model
1. User lands on list view → Apollo query fetches data
2. User clicks row → Sheet opens with detail view
3. User clicks [+ Action] → Dialog/Sheet opens with form
4. User submits form → Mutation → refetch list → Toast confirmation

### State Management
- **Server data**: Apollo query `useGetXxxQuery()` with pagination variables
- **Filters**: URL search params (persisted on refresh)
- **Edit form**: react-hook-form + Zod schema
- **Sheet state**: Zustand dialog store via `createDialogStore()`

### Design Pattern
Follows the **[Pattern Name]** pattern, similar to `features/[ExistingFeature]/` in nash-portal.

### Effort: [S / M / L]

### Trade-offs
- ✅ [What you gain]
- ⚠️ [What you lose or defer]

---

## Proposal B: Standard — [Short name]
[Same structure]

---

## Proposal C: Full — [Short name]
[Same structure]

---

## Recommendation

**Recommended: Proposal [X] — [Name]**

**Rationale**: [Why this is the best balance of effort, value, and Nash patterns]

### Design System Impact
- [ ] New component needed: [Yes/No — if yes, what]
- [ ] New design token needed: [Yes/No — if yes, what]
- [ ] New variant on existing component: [Yes/No — if yes, what]
- [ ] Figma update needed: [Yes/No]
```

### Step 5: Update `_meta.json`
Set `"propose": "complete"` in the pipeline state.

## Wireframe Rules

- Use box-drawing characters: `┌ ┐ └ ┘ ├ ┤ ┬ ┴ ┼ │ ─`
- Show the full page context (sidebar, topbar) so the feature is situated
- Label interactive elements with `[brackets]`
- Use `●` for active/selected states, `○` for inactive
- Keep it readable — don't overcrowd
- Show the most important screen state, not every possible state

## After Completion

Tell the user:
- Summary of the 3 proposals with effort levels
- Your recommendation and why
- Design system implications
- "Approve a proposal to continue to `/nash-spec`, or ask me to revise."
