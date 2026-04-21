# Nash Design Pipeline Skills

## Context

As Nash's product designer, you receive feature requests from many sources (Slack, Linear, Figma, meetings, your own research) and struggle to quickly understand what exactly needs to be built, where it lives in the system, and how to scope it. This pipeline of Claude Code skills creates a structured workflow — inspired by BB-Skills — that transforms raw input into clear specs, design proposals, and implementation plans, all Nash-specific.

## Installation

**Location**: `~/.claude/skills/` (global, available across all Nash projects)

Each skill = one `SKILL.md` file with YAML frontmatter + prompt body.

## The Pipeline (10 skills)

```
Phase 1: Discovery              Phase 2: Execution           Governance
─────────────────────           ──────────────────           ──────────
/nash-ingest                    /nash-spec                   /nash-govern
    ↓                               ↓
/nash-locate                    /nash-tasks
    ↓                               ↓
/nash-clarify                   /nash-prototype
    ↓
/nash-propose
    ↓
── APPROVAL GATE ──────────────────→
```

## Artifact Structure

All output goes to `specs/<NNN>-<feature-name>/` in the current project:

```
specs/001-fleet-bulk-actions/
├── 00-intake.md          ← /nash-ingest output
├── 01-location.md        ← /nash-locate output
├── 02-questions.md       ← /nash-clarify output
├── 03-proposals.md       ← /nash-propose output (with wireframes)
├── 04-spec.md            ← /nash-spec output
├── 05-tasks.md           ← /nash-tasks output
├── 06-prototype/         ← /nash-prototype output (.tsx files)
└── _meta.json            ← Pipeline state tracking
```

## Skill Definitions

### 1. `/nash-ingest` — Digest raw input into structured intake

**Input**: Raw text (pasted Slack messages, meeting transcripts, PRD text, Linear issue IDs, Figma links, user's own notes/research from Claude Code)
**Output**: `00-intake.md`
**Allowed tools**: Read, Grep, Glob, WebFetch, Linear MCP (read-only), Gmail MCP (read-only)

What it does:
- Accepts ANY format of input — messy Slack thread, bullet points, transcript, Linear issue ID, Figma URL
- Extracts and structures: **Problem statement**, **Who requested it** (stakeholder + role), **Target user** (merchant/provider/driver/end-customer), **Business context** (why now), **Constraints** mentioned, **Raw quotes** (preserves original wording)
- Flags ambiguities and unknowns as `[UNCLEAR: ...]`
- If a Linear issue ID is provided → fetches full issue + comments via MCP
- If a Figma link is provided → notes it for `/nash-locate` to process
- Auto-detects which Nash app this likely targets (portal/provider/tracking/store)
- Creates `_meta.json` with feature name, date, source type, pipeline state

### 2. `/nash-locate` — Map feature to Nash architecture

**Input**: `00-intake.md` + codebase exploration
**Output**: `01-location.md`
**Allowed tools**: Read, Grep, Glob (codebase exploration)

What it does:
- Reads the intake and identifies WHERE in the Nash ecosystem this belongs
- Determines: **Target app** (nash-portal, nash-provider, nash-tracking, nash-store)
- Identifies **affected modules** from the actual codebase:
  - Portal: Orders, Fleet, Shipping, Returns, OrgSettings, Zones, Locations, Providers, etc.
  - Provider: Jobs, Contracts, Opportunities, Payments, Analytics, etc.
- Lists **existing screens/routes** that will be modified or extended
- Lists **existing components** from `@usenash/ui` (46) and `@usenash/components` (90+) that are relevant
- Identifies **GraphQL operations** that exist for related data
- Identifies **Zustand stores** or state that will be affected
- Flags if this requires: new route, new module, extension of existing module, or cross-module work
- Produces an **impact map**: files/modules touched, estimated blast radius (small/medium/large)

### 3. `/nash-clarify` — Generate qualifying questions

**Input**: `00-intake.md` + `01-location.md`
**Output**: `02-questions.md`
**Allowed tools**: Read

What it does:
- Analyzes intake + location for gaps and ambiguities
- Generates **max 10 questions** organized by audience:
  - **For PM/Requester**: Scope, priority, success criteria, edge cases
  - **For Engineering**: API availability, data model changes, performance concerns
  - **For Design Lead**: Design system implications, pattern precedents, Figma status
- Each question includes:
  - The question itself
  - **Why it matters** (what decision it unblocks)
  - **Suggested answer** (best guess based on Nash patterns)
  - **Options table** (if applicable)
- Questions are ranked by blocking priority (P0 = blocks everything, P1 = blocks design, P2 = nice to have)
- Marks questions that can be answered by reading code vs. need human input

### 4. `/nash-propose` — Design proposals with wireframes

**Input**: `00-intake.md` + `01-location.md` + `02-questions.md` (answered or not)
**Output**: `03-proposals.md`
**Allowed tools**: Read, Grep, Glob

What it does:
- Produces **2-3 design proposals** at different scope/effort levels (e.g., MVP, Standard, Full)
- Each proposal includes:
  - **Concept summary** (2-3 sentences)
  - **ASCII wireframe** of the key screen(s)
  - **Component bill of materials**: exact Nash components to use (`<DataTable>`, `<Sheet>`, `<Badge>`, etc.)
  - **Interaction model**: what triggers what (clicks, dialogs, navigation)
  - **State approach**: which store pattern (Zustand dialog store, Apollo cache, react-hook-form)
  - **Design pattern recommendation**: which existing Nash pattern this follows (e.g., "follows the Orders list → detail → sheet pattern")
  - **Effort estimate**: S/M/L relative to similar Nash features
- References **existing Nash screens** as precedents ("similar to how Fleet Management works")
- Calls out design system implications (new component needed? new token? new variant?)
- Recommends one proposal with rationale
- Delegates to existing skills where relevant: references `/design-system` for tokens, `/ux-guidelines` for patterns, `/component-mapping` for Figma mapping

### 5. `/nash-spec` — Detailed implementation specification

**Input**: Approved proposal from `03-proposals.md`
**Output**: `04-spec.md`
**Allowed tools**: Read, Grep, Glob

What it does:
- Converts the approved proposal into a detailed implementation spec
- Includes:
  - **File tree**: new/modified files with paths relative to the target app
  - **Component API**: TypeScript interfaces for new components (props, variants)
  - **GraphQL operations**: queries/mutations needed (referencing existing schema patterns)
  - **Zod schemas**: form validation schemas
  - **Route changes**: new routes or modifications to existing routing
  - **State management**: Zustand store definitions, Apollo cache policies
  - **Feature flag**: if this should be behind a flag, its name and default
  - **Accessibility requirements**: keyboard nav, ARIA, screen reader behavior
  - **Test scenarios**: key user flows to verify
- References actual Nash code patterns with file paths

### 6. `/nash-tasks` — Break spec into ordered tasks

**Input**: `04-spec.md`
**Output**: `05-tasks.md`
**Allowed tools**: Read

What it does:
- Decomposes the spec into **dependency-ordered tasks**
- Format: `- [ ] T001 [S] [Phase] Description — file/path.tsx`
- Effort per task: [XS] [S] [M] [L]
- Phases: Setup → Data Layer → Components → Integration → Polish
- Each task is **atomic** — completable in one focused session
- Tasks reference specific files to create/modify
- Marks parallel-safe tasks (can be done simultaneously)
- Includes total effort estimate as sum of task sizes
- Tasks are ready to be manually copied into Linear issues

### 7. `/nash-prototype` — Generate working prototype

**Input**: `04-spec.md` + approved proposal
**Output**: `06-prototype/` directory with .tsx files
**Allowed tools**: Read, Grep, Glob, Write

What it does:
- Generates **working React component files** using:
  - Actual `@usenash/ui` imports (`Button`, `Card`, `DataTable`, `Sheet`, etc.)
  - Nash design tokens (semantic colors, spacing scale)
  - Mock data matching the expected GraphQL schema shape
  - react-hook-form + Zod for any forms
  - Proper TypeScript types
- Files are runnable in any PRO-XXX Vite project
- Includes a `README.md` with setup instructions and what to look for
- NOT production code — it's a testable design artifact for validation

### 8. `/nash-govern` — Design system governance model

**Input**: None (standalone) or a specific question/decision
**Output**: `governance/constitution.md` (one-time, then updated)
**Allowed tools**: Read, Grep, Glob

What it does:
- Creates/maintains the Nash design governance document covering:
  - **Component decision tree**: When to use `@usenash/ui` vs `@usenash/components` vs create new
  - **Token rules**: When to add new tokens vs use existing, naming conventions
  - **Pattern registry**: Approved UI patterns in Nash (list→detail, form→sheet, filter→table, etc.)
  - **Figma-code sync rules**: What must match exactly, what can deviate
  - **Review checklist**: What to check before merging UI changes
  - **Variant policy**: When to add a variant vs create a new component
  - **Deprecation process**: How to sunset a component or pattern
- Answers questions like "should I use Sheet or Dialog here?" with Nash-specific decision logic
- Can be invoked standalone to review/update the governance doc

### 9. `/nash-audit` — Audit code for design system compliance

**Input**: File paths or a feature directory to audit
**Output**: Audit report (printed to conversation)
**Allowed tools**: Read, Grep, Glob

What it does:
- Scans specified files/directories for Nash design system violations:
  - Hardcoded colors instead of tokens
  - Arbitrary Tailwind values (`[#fff]`, `[480px]`)
  - Missing semantic tokens
  - Opacity modifiers on colors
  - Margin usage instead of flex/gap
  - Non-standard component patterns
  - Missing accessibility attributes
  - Inconsistent spacing
- References governance rules from `/nash-govern`
- Produces a severity-ranked findings table
- Suggests specific fixes for each violation

### 10. `/nash-sync` — Figma-to-code sync check

**Input**: Optional Figma file/frame link
**Output**: Sync report (printed to conversation)
**Allowed tools**: Read, Grep, Glob, Figma MCP tools

What it does:
- Compares current code components against Figma source
- Detects drift: components that have diverged from Figma specs
- Lists unmapped components (in Figma but not in Code Connect)
- Suggests Code Connect mappings to create
- Delegates to existing `/code-connect` skill for actual mapping work

---

## Files to Create

| # | File | Path |
|---|------|------|
| 1 | nash-ingest | `~/.claude/skills/nash-ingest/SKILL.md` |
| 2 | nash-locate | `~/.claude/skills/nash-locate/SKILL.md` |
| 3 | nash-clarify | `~/.claude/skills/nash-clarify/SKILL.md` |
| 4 | nash-propose | `~/.claude/skills/nash-propose/SKILL.md` |
| 5 | nash-spec | `~/.claude/skills/nash-spec/SKILL.md` |
| 6 | nash-tasks | `~/.claude/skills/nash-tasks/SKILL.md` |
| 7 | nash-prototype | `~/.claude/skills/nash-prototype/SKILL.md` |
| 8 | nash-govern | `~/.claude/skills/nash-govern/SKILL.md` |
| 9 | nash-audit | `~/.claude/skills/nash-audit/SKILL.md` |
| 10 | nash-sync | `~/.claude/skills/nash-sync/SKILL.md` |

## Implementation Order

1. **Phase 1 foundation** (immediate value): `nash-ingest`, `nash-locate`, `nash-govern`
2. **Phase 1 completion**: `nash-clarify`, `nash-propose`
3. **Phase 2**: `nash-spec`, `nash-tasks`, `nash-prototype`
4. **Maintenance**: `nash-audit`, `nash-sync`

## Verification

After creating each skill:
1. Run `/nash-ingest` with a real feature request to test intake parsing
2. Run `/nash-locate` on the output to verify it finds correct Nash modules
3. Run the full Phase 1 pipeline on a known feature to validate end-to-end
4. Test `/nash-govern` standalone to verify it produces a useful governance doc
5. Test `/nash-audit` on an existing Nash component directory
