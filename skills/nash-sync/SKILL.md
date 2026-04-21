---
name: nash-sync
description: Check Figma-to-code synchronization — detect drift, unmapped components, and token mismatches. Use to keep Figma and code aligned.
argument-hint: Optional Figma file/frame URL
allowed-tools: Read, Grep, Glob, Agent
---

# Nash Sync — Figma-to-Code Synchronization Check

You are a design-engineering bridge at Nash. Your job is to detect drift between Figma designs and the code implementation, and recommend sync actions.

## What This Checks

1. **Component coverage**: Are all Figma components mapped to code components?
2. **Token alignment**: Do CSS variables match Figma variables?
3. **Code Connect status**: Which components have Code Connect mappings?
4. **Visual drift**: Components that have changed in code but not Figma (or vice versa)

## Execution Steps

### Step 1: Inventory code components

Scan the codebase for:
- All components in `@usenash/ui` (packages/ui/src/components/)
- All components in `@usenash/components` (packages/components/src/)
- All components in Storybook (nash-storybook/src/components/)
- Any Code Connect configuration files

### Step 2: Check design tokens

Read the CSS variables from:
- `apps/nash-portal/src/index.css` (or equivalent)
- `nash-storybook/src/tokens/global.css`
- Tailwind config files

Compare light and dark theme token values. Flag any discrepancies between apps.

### Step 3: Check Code Connect mappings

Look for existing Code Connect configurations (`.figma.tsx` files or code-connect config). List which components are connected and which are not.

### Step 4: If a Figma URL is provided

Note: This step requires Figma MCP tools to be available. If they're not configured, skip and note it in the report.

- Fetch the Figma file's component list
- Compare against code component inventory
- Flag components that exist in one but not the other

### Step 5: Produce the sync report

## Output Format (printed to conversation)

```markdown
# Nash Figma-Code Sync Report

**Date**: [today]
**Code components**: [N] in @usenash/ui + [N] in @usenash/components
**Storybook components**: [N] ported
**Code Connect mappings**: [N] connected / [N] total

---

## Token Alignment

### Cross-App Token Check
| Token | Portal value | Storybook value | Match? |
|-------|-------------|-----------------|--------|
| --primary | [value] | [value] | ✅/❌ |
| --background | [value] | [value] | ✅/❌ |
| ... | ... | ... | ... |

### Missing Tokens
Tokens defined in one place but not another:
- [token name] — exists in [A] but not [B]

---

## Component Coverage

### In @usenash/ui but NOT in Storybook
- [Component] — needs porting

### In Storybook but NOT in @usenash/ui
- [Component] — may be storybook-only or needs backporting

### Code Connect Status
| Component | Has Code Connect? | Action needed |
|-----------|-------------------|---------------|
| Button | ✅ | None |
| DataTable | ❌ | Create mapping |
| ... | ... | ... |

---

## Recommendations

### Priority 1: Token fixes
[List any token mismatches that need immediate fixing]

### Priority 2: Missing Code Connect
[Components that would benefit most from Code Connect mappings]

### Priority 3: Component porting
[Components that need to be ported to/from Storybook]

---

## Next Steps
- Run `/code-connect` to create mappings for unmapped components
- Update tokens in [file] to match [source of truth]
```

## After Completion

Tell the user:
- Overall sync status (healthy / needs attention / critical drift)
- Top priority fixes
- "Use `/code-connect` to fix Code Connect mappings, or manually update tokens as listed."
