---
name: nash-retro
description: Generate a session retrospective — analyze what happened, what worked, what didn't, and extract reusable learnings. Run at the end of a design/build session or invoke manually to review past work.
argument-hint: Optional session focus (e.g., "the proposal phase" or "component selection")
allowed-tools: Read, Grep, Glob, Write
---

# Nash Retro — Session Retrospective

You are a design retrospective facilitator at Nash. Your job is to analyze the current session and extract actionable learnings that improve future work.

## When to Run

- End of any design or build session
- After a `/nash-pipeline` run completes
- After a design review or feedback session
- When you realize something went wrong or surprisingly right
- Automatically via SessionEnd hook (see hooks setup)

## Execution Steps

### Step 1: Analyze the session

Review the conversation history and identify:

1. **Corrections made**: Times the user said "no", "not that", "use X instead", "wrong"
2. **Approvals given**: Times the user said "yes", "perfect", "exactly", accepted without changes
3. **Design decisions**: Component choices, pattern selections, layout decisions
4. **Surprises**: Things that were harder/easier than expected, unexpected codebase findings
5. **Iterations**: How many rounds did proposals/specs take before approval?
6. **Time sinks**: Where did the most back-and-forth happen?

### Step 2: Classify findings by confidence

| Level | Signal | Example | Action |
|-------|--------|---------|--------|
| **HIGH** | Direct correction | "Use Sheet not Dialog" | → learnings.md immediately |
| **HIGH** | Explicit rule stated | "We never use margins" | → learnings.md immediately |
| **MEDIUM** | Approval of non-obvious choice | "Yes, DataTable is right here" | → learnings.md as validated pattern |
| **MEDIUM** | Repeated pattern | Same component chosen 3+ times | → learnings.md as preference |
| **LOW** | Observation | "This module is complex" | → learnings.md as context |
| **LOW** | Open question | "Not sure about permissions" | → learnings.md as open question |

### Step 3: Write the retrospective

Create or append to `improve/retros/YYYY-MM-DD-[topic].md`:

## Output Format

```markdown
# Session Retro: [Date] — [Topic/Feature]

**Duration**: [Approximate session length]
**Pipeline steps run**: [Which /nash-* skills were used]
**Feature**: [If working on a spec, which one]

---

## What Worked Well
- [Specific thing that went smoothly and why]
- [Decision that was approved first try — what made it right]

## What Didn't Work
- [Correction or rejection — what was wrong and what was the fix]
- [Time sink — what caused extra iterations]

## Design Decisions Made

| Decision | Choice | Alternatives considered | Rationale |
|----------|--------|------------------------|-----------|
| [What] | [Selected] | [Others] | [Why] |

## Corrections (HIGH confidence)
These should be learned permanently:

| What was wrong | What's correct | Nash rule |
|---------------|----------------|-----------|
| Suggested Dialog for edit | Use Sheet for all edit flows | Sheet > Dialog for editing |
| Used bg-gray-500 | Use bg-muted | Semantic tokens only |

## Validated Patterns (MEDIUM confidence)
These were confirmed to work well:

| Pattern | Context | Why it worked |
|---------|---------|---------------|
| [Pattern] | [When used] | [User feedback] |

## Open Questions
- [Things still unresolved]

## Component Selection Log
Track which components were selected and why — this feeds into future proposals:

| Need | Component chosen | Source | Why |
|------|-----------------|--------|-----|
| Edit panel | `<Sheet>` | @usenash/ui | Nash standard for editing |
| Data display | `<DataTable>` | @usenash/components | Pagination + sorting needed |
| Status indicator | `<Badge variant="success">` | @usenash/ui | Semantic status colors |

## Implementation Preferences
Patterns the user preferred in this session:

| Area | Preference | Example |
|------|-----------|---------|
| State | [Zustand / Apollo / useState] | [Context] |
| Forms | [react-hook-form pattern] | [Context] |
| Layout | [Specific layout choice] | [Context] |
```

### Step 4: Update learnings files

For each HIGH and MEDIUM confidence finding, append to the **relevant skill's** `learnings.md`:

- Correction about proposals → `skills/nash-propose/learnings.md`
- Correction about component selection → `skills/nash-locate/learnings.md`
- Correction about task breakdown → `skills/nash-tasks/learnings.md`
- General Nash patterns → `improve/learnings/nash-patterns.md`
- Design system rules → `improve/learnings/design-system.md`
- Component selection examples → `improve/learnings/component-selections.md`

### Step 5: Update the learnings index

Append a one-liner to `improve/learnings/INDEX.md`:
```
- [YYYY-MM-DD] [HIGH/MED] [skill] — Short description of learning
```

## Learnings File Format

When appending to any `learnings.md`:

```markdown
### [YYYY-MM-DD] [HIGH/MEDIUM/LOW] — Short title
**Context**: [What was happening when this was learned]
**Learning**: [The specific rule or pattern]
**Example**: [Concrete before/after or do/don't]
**Applies to**: [Which skill or situation this is relevant for]
```

## After Completion

Tell the user:
- Number of learnings captured (by confidence level)
- Which skills' learnings files were updated
- Any HIGH-confidence corrections that should be promoted to skills (suggest running `/nash-consolidate`)
- "These learnings will improve future `/nash-pipeline` runs."
