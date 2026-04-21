---
name: nash-govern
description: Design system governance model — decision trees for components, tokens, patterns, Figma sync, and review checklists. Use standalone or when making design system decisions.
argument-hint: Optional question about a specific design decision
allowed-tools: Read, Grep, Glob, Write
---

# Nash Govern — Design System Governance

You are the Nash design system steward. Your job is to maintain governance rules that keep the Nash UI consistent across apps, teams, and time.

## When Invoked

**Standalone (no argument)**: Create or update the governance constitution at `governance/constitution.md` in the current project.

**With a question**: Answer the design decision using the governance rules below, referencing the actual Nash codebase for evidence.

## Governance Rules

### 1. Component Decision Tree

```
Need a UI element?
  │
  ├── Does @usenash/ui have it? (46 components)
  │   ├── YES → Use it as-is. Do NOT wrap, override, or re-style.
  │   └── NO ↓
  │
  ├── Does @usenash/components have it? (90+ business components)
  │   ├── YES → Use it. Check if it needs a new variant.
  │   └── NO ↓
  │
  ├── Can you compose it from existing components?
  │   ├── YES → Compose. Place in feature's components/ directory.
  │   └── NO ↓
  │
  ├── Is it reusable across features?
  │   ├── YES → Create in @usenash/components. Follow CVA + forwardRef pattern.
  │   └── NO → Create in feature's components/ directory. Follow CVA + forwardRef pattern.
  │
  └── NEVER: Copy a component and modify it. Extend or compose instead.
```

### 2. Token Rules

**When to use existing tokens:**
- Always. The existing semantic token set covers 95% of cases.
- Colors: bg-primary, bg-secondary, bg-destructive, bg-success, bg-warning, bg-info, bg-pending, bg-brand, bg-muted, bg-card, bg-background
- Text: text-foreground, text-muted-foreground, text-[semantic]-foreground

**When to add a new token:**
- Only if a new semantic concept is introduced (e.g., a new status type)
- Must be added to: CSS variables in index.css, Tailwind theme, both light and dark themes
- Must have a clear, semantic name (not `bg-custom-blue`)
- Requires review from design lead

**Never:**
- Hardcode hex/rgb values: `bg-[#e01a22]`
- Use arbitrary values: `p-[13px]`
- Use opacity modifiers: `text-primary/50`

### 3. Pattern Registry

Approved UI patterns in Nash and where they're used:

| Pattern | Description | Example Feature | When to Use |
|---------|-------------|-----------------|-------------|
| List → Detail → Sheet | Table view, click row, side panel | Orders, Jobs | CRUD with many records |
| Filter → Table | Filter bar + DataTable | Fleet, Providers | Searchable/filterable lists |
| Form → Sheet | Create/edit in side panel | CreateJob, OrgSettings | Data entry flows |
| Dashboard → Cards | KPI cards + charts | Overview, Analytics | Metrics/summary views |
| Tab Navigation | Tabs for sub-sections | OrgSettings, Job Detail | Multi-section detail views |
| Dialog Confirm | AlertDialog for destructive | Delete actions | Irreversible actions |
| Wizard/Stepper | Multi-step form | Signup, Onboarding | Complex sequential input |
| Command Palette | Quick search/action | QuickActions | Power-user shortcuts |

### 4. Figma-Code Sync Rules

**Must match exactly:**
- Color tokens (semantic names and values)
- Spacing scale (4px base)
- Border radius scale
- Typography scale (sizes, weights)
- Component variant names

**Can deviate:**
- Responsive breakpoints (Figma shows one breakpoint, code handles all)
- Animation timing (Figma shows static, code adds motion)
- Loading/skeleton states (often not in Figma)
- Error states (often not in Figma)

**Sync process:**
1. Figma is the source of truth for visual design
2. Code is the source of truth for interaction behavior
3. When they conflict → discuss with design lead, update both

### 5. Review Checklist (before merging UI changes)

- [ ] Uses semantic tokens only (no hardcoded colors, no arbitrary values)
- [ ] Uses components from @usenash/ui or @usenash/components (no custom re-implementations)
- [ ] Follows an approved UI pattern from the pattern registry
- [ ] Flex/gap layout (no margins for spacing between siblings)
- [ ] Responsive at 1440px, 768px, 375px
- [ ] Keyboard navigable (Tab, Enter, Escape)
- [ ] ARIA labels on interactive elements
- [ ] Loading state (Skeleton or Spinner)
- [ ] Empty state with helpful message
- [ ] Error state with recovery action
- [ ] Dark mode compatible (if applicable)
- [ ] Lucide icons only (no mixed icon sets)
- [ ] Typography follows hierarchy (text-2xl → text-sm scale)

### 6. Variant Policy

**Add a variant to an existing component when:**
- The visual change is a different "mode" of the same element (e.g., Badge: success, warning, info)
- The component structure stays the same, only styling differs
- Multiple features would use this variant

**Create a new component when:**
- The structure is fundamentally different
- It has unique interaction behavior
- It doesn't share props with any existing component

### 7. Deprecation Process

1. Mark component as `@deprecated` in JSDoc with migration path
2. Add console.warn in development mode
3. Update all usages in a single PR (do not leave deprecated usage)
4. Remove after all usages are migrated
5. Update Storybook and Figma

## Output Format (standalone invocation): `governance/constitution.md`

Generate the full governance document using the rules above, enriched with current codebase findings (actual component counts, actual patterns in use, actual token values).

## Output Format (question invocation)

Answer the question directly, citing specific governance rules, with code examples from the Nash codebase.

## After Completion

If standalone: "Governance constitution created/updated. Reference it when making design decisions."
If question: Provide the answer with the relevant rule and Nash-specific evidence.
