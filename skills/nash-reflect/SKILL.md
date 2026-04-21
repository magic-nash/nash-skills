---
name: nash-reflect
description: Capture a specific learning or insight mid-session. Quick way to record do's, don'ts, patterns, and component selections without running a full retro. Use whenever you notice something worth remembering.
argument-hint: The learning to capture (e.g., "Sheet not Dialog for editing" or "DataTable needs explicit column widths for this use case")
allowed-tools: Read, Write
---

# Nash Reflect — Quick Learning Capture

You are a learning capture tool for Nash design work. Your job is to quickly record a single insight, pattern, or correction so it's not lost.

## When to Use

- User corrects your component choice → `/nash-reflect "Use Sheet not Dialog for editing"`
- You discover a codebase pattern → `/nash-reflect "Orders uses SavedViews for filter persistence"`
- A design decision is made → `/nash-reflect "Merchants prefer tab navigation over sidebar for settings"`
- Something fails → `/nash-reflect "Don't use arbitrary values in Tailwind"`

## Execution Steps

### Step 1: Classify the input

Analyze the user's input and classify:

**Type** (pick one):
- `CORRECTION` — Something was wrong, here's what's right (do/don't)
- `PATTERN` — A reusable approach that works well
- `PREFERENCE` — User/team preference for how things should be done
- `DISCOVERY` — Something found in the codebase worth remembering
- `EXAMPLE` — A good implementation to reference in the future
- `AVOID` — An anti-pattern or approach that doesn't work

**Confidence**: HIGH (explicit correction/rule) / MEDIUM (validated choice) / LOW (observation)

**Target skill**: Which nash skill this learning applies to:
- `nash-ingest` — How to parse/understand requests
- `nash-locate` — Where things live in Nash, architecture knowledge
- `nash-clarify` — What questions to ask, what gaps to look for
- `nash-propose` — Design patterns, wireframe approaches, component choices
- `nash-spec` — Implementation details, API patterns, technical decisions
- `nash-tasks` — Task breakdown, effort estimation, dependency patterns
- `nash-prototype` — Code patterns, prototyping approaches
- `nash-govern` — Design system rules, governance decisions
- `general` — Applies across all skills

### Step 2: Write the learning

Append to the target skill's `learnings.md` file.

If the file doesn't exist, create it with this header:
```markdown
# Learnings: [Skill Name]

> This file is automatically maintained by /nash-reflect and /nash-retro.
> Entries are appended chronologically. Run /nash-consolidate to promote
> recurring patterns into the skill's main instructions.

---
```

Then append the entry:

```markdown
### [YYYY-MM-DD] [TYPE] [CONFIDENCE] — [Short title]
**Learning**: [Clear, specific statement of what to do or not do]
**Context**: [What was happening when this was captured]
**Example**:
- DO: `[correct approach with code/component example]`
- DON'T: `[incorrect approach that was corrected]`
**Tags**: #[component|pattern|token|layout|state|a11y|interaction]
```

### Step 3: Update the index

Append to `improve/learnings/INDEX.md`:
```
- [YYYY-MM-DD] [CONFIDENCE] [skill] [TYPE] — Short description
```

If `improve/learnings/INDEX.md` doesn't exist, create it:
```markdown
# Nash Learnings Index

> Chronological log of all captured learnings across skills.
> Run /nash-consolidate to review and promote patterns.

---
```

### Step 4: Check for patterns

After writing, quickly scan the target skill's `learnings.md` for:
- **Repeated themes**: 3+ entries about the same topic → flag for consolidation
- **Contradictions**: New entry conflicts with an older one → flag for resolution

## Shorthand Support

The user can use quick shorthands:

| Input | Interpreted as |
|-------|---------------|
| `"Sheet not Dialog"` | CORRECTION, HIGH, nash-propose |
| `"DataTable works great for fleet list"` | PATTERN, MEDIUM, nash-propose |
| `"Never use bg-[#xxx]"` | AVOID, HIGH, nash-govern |
| `"Orders module uses SavedViews"` | DISCOVERY, MEDIUM, nash-locate |
| `"The Fleet list page is a great reference"` | EXAMPLE, MEDIUM, nash-propose |

## After Completion

Confirm in one line:
```
✅ Captured [TYPE] learning → [skill]/learnings.md ([N] total entries for this skill)
```

If pattern detected: "This is the [N]th entry about [topic]. Consider running `/nash-consolidate` to promote it."
