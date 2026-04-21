# Nash Skills

A collection of [Claude Code](https://claude.ai/claude-code) skills for Product Designers and Design Engineers at Nash. Transforms raw feature requests from any source into structured specs, design proposals, and implementation plans — and gets smarter over time.

Inspired by [BB-Skills](https://github.com/buildbetter-app/BB-Skills).

---

## What It Does

You're a product designer. People ask you to build things via Slack, Linear, meetings, Figma comments, PRDs. The challenge: understanding what exactly needs to be built, where it lives in Nash, and how to scope it.

Nash Skills solves this with **14 slash commands** organized into 3 pipelines:

### Design Pipeline — From idea to prototype

| Step | Skill | What it produces |
|------|-------|-----------------|
| 1 | `/nash-ingest` | Structured intake from any raw input (Slack, transcript, PRD, Linear issue) |
| 2 | `/nash-locate` | Architecture map — which app, module, screens, components, user flows, edge cases |
| 3 | `/nash-clarify` | Qualifying questions ranked by priority, organized by audience (PM, Eng, Design) |
| 4 | `/nash-propose` | 2-3 design proposals with ASCII wireframes and Nash component bills of materials |
| | | **--- approval gate ---** |
| 5 | `/nash-spec` | Implementation spec — file tree, component APIs, GraphQL, Zod schemas, routes |
| 6 | `/nash-tasks` | Dependency-ordered task list with effort estimates, ready to copy into Linear |
| 7 | `/nash-prototype` | Working .tsx files using real `@usenash/ui` imports, runnable in any PRO-XXX project |

### Governance Pipeline — Keep the design system consistent

| Skill | What it does |
|-------|-------------|
| `/nash-govern` | Design system constitution — decision trees for components, tokens, patterns, Figma sync |
| `/nash-audit` | Scans code for design system violations (hardcoded colors, wrong components, bad spacing) |
| `/nash-sync` | Detects Figma-to-code drift, lists unmapped Code Connect components |

### Improvement Pipeline — Skills that learn from your work

| Skill | When to use | What it does |
|-------|------------|-------------|
| `/nash-reflect` | Mid-session | Quick capture: "Use Sheet not Dialog" → saved to skill's learnings |
| `/nash-retro` | End of session | Full retrospective — corrections, validated patterns, component decisions |
| `/nash-consolidate` | Weekly | Promotes repeated learnings into skill instructions — skills get smarter |

Plus **`/nash-pipeline`** — a single entry point that asks what you need and runs the selected steps sequentially.

---

## What's Inside

### 14 Skills

Each skill is a `SKILL.md` file with YAML frontmatter and detailed prompt instructions. They're Nash-specific — they know about the apps, modules, components, design tokens, and architecture patterns.

```
skills/
│
│  Design Pipeline
├── nash-pipeline/SKILL.md      # Orchestrator — single entry point with presets
├── nash-ingest/SKILL.md        # Digest raw input into structured intake
├── nash-locate/SKILL.md        # Map to Nash apps, modules, screens, user flows
├── nash-clarify/SKILL.md       # Generate qualifying questions
├── nash-propose/SKILL.md       # Design proposals with wireframes
├── nash-spec/SKILL.md          # Implementation specification
├── nash-tasks/SKILL.md         # Task breakdown for Linear
├── nash-prototype/SKILL.md     # Working React prototype
│
│  Governance
├── nash-govern/SKILL.md        # Design system rules & decision trees
├── nash-audit/SKILL.md         # Code compliance audit
├── nash-sync/SKILL.md          # Figma-code sync check
│
│  Improvement
├── nash-reflect/SKILL.md       # Quick mid-session learning capture
├── nash-retro/SKILL.md         # Session retrospective
└── nash-consolidate/SKILL.md   # Promote learnings into skills
```

### Learnings Infrastructure

The improvement pipeline writes to shared knowledge files that accumulate over time:

```
improve/
├── learnings/
│   ├── INDEX.md                # Chronological log of all learnings
│   ├── consolidated.md         # "Nash Design Bible" — promoted knowledge
│   ├── nash-patterns.md        # General Nash UI/UX patterns
│   ├── design-system.md        # Design system do's and don'ts
│   └── component-selections.md # Which component for which need (with examples)
└── retros/                     # Session retrospective files
```

### Artifact Output

Each design pipeline run produces artifacts in a numbered spec directory:

```
specs/001-fleet-bulk-actions/
├── 00-intake.md                # Structured feature request
├── 01-location.md              # Architecture map + user flows + edge cases
├── 02-questions.md             # Qualifying questions for the team
├── 03-proposals.md             # Design proposals with wireframes
├── 04-spec.md                  # Implementation specification
├── 05-tasks.md                 # Linear-ready task list
├── 06-prototype/               # Working .tsx files
└── _meta.json                  # Pipeline state tracking
```

---

## How It Works

### Design Pipeline Flow

```
Phase 1: Discovery                    Phase 2: Execution
─────────────────────                 ──────────────────
/nash-ingest                          /nash-spec
    ↓                                     ↓
/nash-locate                          /nash-tasks
    ↓                                     ↓
/nash-clarify                         /nash-prototype
    ↓
/nash-propose
    ↓
── APPROVAL GATE ────────────────────────→
```

**Phase 1** takes any messy input and produces clear design proposals. **Phase 2** takes an approved proposal and produces implementation-ready artifacts. The approval gate ensures you validate with stakeholders before committing to implementation details.

### Improvement Flywheel

```
During session              End of session           Weekly
────────────────            ──────────────           ──────
/nash-reflect               /nash-retro              /nash-consolidate
    │                           │                        │
    ▼                           ▼                        ▼
skill/learnings.md ◄──── improve/retros/ ────► Promote to SKILL.md
                          improve/learnings/        consolidated.md
```

1. **Reflect** — You correct something ("Use Sheet, not Dialog") → captured to the relevant skill's `learnings.md`
2. **Retro** — End-of-session analysis extracts corrections, validated patterns, and component selection decisions
3. **Consolidate** — Reviews all learnings, removes stale entries, promotes patterns that appear 3+ times into the skill's actual SKILL.md instructions

Result: skills literally get smarter over time. Fewer corrections needed with each session.

### What `/nash-locate` Produces (example)

The location skill goes beyond file mapping. It produces:

- **Architecture map** — Target app, affected modules, screens, routes, components, GraphQL operations
- **Users & interactions** — User types with Primary/Secondary/Tertiary priority indicators
- **Use cases (up to 10)** — "As a [user] I want to [action] so that [outcome]"
- **User flows (max 5)** — Step-by-step: `User clicks X >> System shows Y >> User does Z`
- **Edge cases & fallbacks** — Empty states, large datasets, permissions, network failures, each mapped to a Nash component
- **Impact assessment** — Blast radius, precedent features, architecture recommendation

### Nash Ecosystem Knowledge

All skills are embedded with Nash-specific knowledge:

| Area | What skills know |
|------|-----------------|
| **Apps** | nash-portal (merchant), nash-provider (fleet), nash-tracking (public), nash-store |
| **Modules** | 40+ portal features, 15+ provider screens — exact paths and purposes |
| **Components** | 46 `@usenash/ui` + 90+ `@usenash/components` — names, sources, when to use each |
| **Patterns** | List→Detail→Sheet, Filter→Table, Form→Sheet, Dashboard→Cards, Tab Navigation |
| **Tokens** | Semantic colors, spacing scale, typography hierarchy, border radius |
| **Architecture** | Apollo + GraphQL, Zustand (createDialogStore), react-hook-form + Zod, Tailwind v4, CVA |

---

## Skill Reference

### `/nash-ingest` — Digest raw input
Accepts ANY format: Slack messages, meeting transcripts, PRDs, Linear issue IDs, Figma links, your own research notes. Extracts problem statement, stakeholder, target user, business context, constraints, and raw quotes. Flags ambiguities as `[UNCLEAR: ...]`. Auto-detects target Nash app.

### `/nash-locate` — Map to Nash architecture
Explores the actual codebase to find: target app, affected modules, existing screens/routes, relevant components, GraphQL operations, Zustand stores. Maps user types with priority indicators, generates up to 10 use cases and 5 user flows, lists edge cases with fallback strategies. Produces blast radius assessment.

### `/nash-clarify` — Generate qualifying questions
Max 10 questions organized by audience (PM, Engineering, Design Lead). Each includes: why it matters, suggested answer, options table. Ranked P0 (blocks everything) through P2 (nice-to-have). Flags which can be answered by reading code vs. need human input.

### `/nash-propose` — Design proposals
2-3 proposals at MVP/Standard/Full scope. Each has: ASCII wireframe, component bill of materials (exact Nash components), interaction model, state management approach, design pattern reference, effort estimate. Recommends one with rationale.

### `/nash-spec` — Implementation specification
Converts approved proposal into: file tree, TypeScript component interfaces, GraphQL operations, Zod schemas, route changes, Zustand store definitions, feature flag config, accessibility requirements, test scenarios. References actual Nash code patterns.

### `/nash-tasks` — Task breakdown
Dependency-ordered tasks: `- [ ] T001 [S] [Phase] Description — file/path.tsx`. Phases: Setup → Data Layer → Components → Integration → Polish. Marks parallel-safe tasks. Includes dependency graph and total effort estimate. Ready to copy into Linear.

### `/nash-prototype` — Working prototype
Generates .tsx files using real `@usenash/ui` imports, Nash design tokens, realistic mock data, react-hook-form + Zod, proper TypeScript. Includes all states (loading, empty, error). Runnable in any PRO-XXX Vite project. Not production code — a testable design artifact.

### `/nash-govern` — Governance model
Component decision tree (use/extend/compose/create), token rules, pattern registry, Figma-code sync rules, review checklist, variant policy, deprecation process. Invoke standalone or with a specific question ("Sheet vs Dialog?").

### `/nash-audit` — Compliance audit
Scans files for 8 violation categories: colors, spacing, components, typography, layout, icons, accessibility, patterns. Severity-ranked findings with specific fixes. Compliance score and top 3 quick wins.

### `/nash-sync` — Figma-code sync
Inventories code components vs Figma, checks token alignment across apps, lists Code Connect mapping status. Detects drift and recommends sync actions.

### `/nash-reflect` — Quick learning capture
One-liner: `/nash-reflect "Use Sheet not Dialog"`. Auto-classifies as correction/pattern/preference/discovery/avoid. Assigns confidence level and target skill. Appends to the right `learnings.md`. Flags when 3+ entries suggest consolidation.

### `/nash-retro` — Session retrospective
Analyzes conversation for corrections (HIGH), approvals (MEDIUM), observations (LOW). Produces: what worked, what didn't, design decisions made, component selection log, implementation preferences. Updates learnings files automatically.

### `/nash-consolidate` — Promote learnings
Reviews all `learnings.md` files. Promotes 3+ HIGH-confidence entries into SKILL.md instructions. Archives stale entries. Resolves contradictions. Updates the consolidated "Nash Design Bible". Shows proposed edits for approval before applying.

### `/nash-pipeline` — Orchestrator
Single entry point. Asks what you need with presets:
- **Quick scope** — Ingest + Locate
- **Full discovery** — Ingest + Locate + Clarify + Propose
- **All in one** — Everything with approval gate
- **Custom** — Pick individual steps

Runs selected steps sequentially with progress display and approval gates.

---

## Getting Started

### Requirements

- [Claude Code](https://claude.ai/claude-code) CLI, Desktop app, or IDE extension
- For Linear integration: Linear MCP server configured in Claude Code
- For Figma sync: Figma Developer MCP configured
- Best results when run inside a Nash monorepo (nash-ui / pro-008-nash)

### Install

```bash
# Clone the repo
git clone https://github.com/magic-nash/nash-skills.git

# Copy all skills to your global Claude Code skills directory
cp -r nash-skills/skills/nash-* ~/.claude/skills/
```

Skills auto-load in every new Claude Code session. No registration needed.

### Verify

Open Claude Code and type `/nash-` — you should see all 14 skills in the autocomplete menu.

### Your First Run

```
/nash-pipeline
```

Select "Full discovery", paste a feature request, and watch it go.

### Example Workflows

**Someone asks you to build something:**
```
/nash-pipeline → "Full discovery" → paste the Slack message
→ structured intake → architecture map → questions → design proposals
```

**Quick architecture check:**
```
/nash-pipeline → "Quick scope" → paste the request
→ which app, module, screens, components, user flows
```

**Implementation planning (after design approval):**
```
/nash-spec → /nash-tasks → /nash-prototype
```

**Design system governance:**
```
/nash-govern "Should this be a new component or a Badge variant?"
/nash-audit features/Fleet/
/nash-sync
```

**Capture learnings:**
```
/nash-reflect "Always use DataTable for paginated lists, not custom Table"
/nash-retro                    # end of session
/nash-consolidate              # weekly
```
