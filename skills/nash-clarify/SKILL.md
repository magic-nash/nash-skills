---
name: nash-clarify
description: Generate qualifying questions for the team organized by audience and priority. Use after /nash-ingest and /nash-locate to identify gaps before designing.
argument-hint: Optional path to spec directory (defaults to latest in specs/)
allowed-tools: Read, Write
---

# Nash Clarify — Generate Qualifying Questions

You are a product design consultant at Nash. Your job is to analyze the intake and location documents and generate precise, actionable questions that will unblock the design process.

## Execution Steps

### Step 1: Read prior artifacts
Read `00-intake.md` and `01-location.md` from the spec directory. If no path is given, find the latest spec directory.

### Step 2: Analyze for gaps
Scan for these common gap categories:

1. **Scope boundaries** — What's in vs out? MVP vs full?
2. **User flows** — How does the user get here? What happens after?
3. **Data availability** — Does the API support this? What fields exist?
4. **Edge cases** — Empty states, errors, permissions, bulk operations
5. **Design system** — New component needed? Existing pattern to follow?
6. **Cross-feature** — Does this affect other modules?
7. **Business rules** — Validation, permissions, org-level settings
8. **Performance** — Data volume, pagination, real-time updates
9. **Mobile/responsive** — Must this work on mobile?
10. **Internationalization** — Translation needed?
11. **Success criteria** — How do we know this worked?

### Step 3: Generate questions

Produce **max 10 questions**, organized by audience and ranked by blocking priority.

## Output Format: `02-questions.md`

```markdown
# Qualifying Questions: [Feature Name]

**Total questions**: [N]
**Blocking (P0)**: [N] | **Design-blocking (P1)**: [N] | **Nice-to-have (P2)**: [N]

---

## For PM / Requester

### Q1. [Question] `P0`
**Why it matters**: [What decision this unblocks]
**Suggested answer**: [Your best guess based on Nash patterns]
**Options**:
| Option | Implication |
|--------|------------|
| A) [Option] | [What this means for implementation] |
| B) [Option] | [What this means for implementation] |

**Can be answered by reading code?** [Yes/No]

---

### Q2. [Question] `P1`
...

---

## For Engineering

### Q3. [Question] `P0`
**Why it matters**: [What decision this unblocks]
**Suggested answer**: [Your best guess based on Nash patterns]
**Can be answered by reading code?** [Yes — check `[file path]`]

---

## For Design Lead

### Q4. [Question] `P1`
**Why it matters**: [What decision this unblocks]
**Suggested answer**: [Your best guess based on Nash patterns]

---

## Summary Table

| # | Question (short) | Audience | Priority | Code-answerable? |
|---|-----------------|----------|----------|-------------------|
| Q1 | [Short version] | PM | P0 | No |
| Q2 | [Short version] | PM | P1 | No |
| Q3 | [Short version] | Eng | P0 | Yes |
| Q4 | [Short version] | Design | P1 | No |

---

## Questions Answered by Code
The following questions can potentially be answered by exploring the codebase:
- Q3: Check `[specific file/directory]` for [what to look for]
```

### Step 4: Update `_meta.json`
Set `"clarify": "complete"` in the pipeline state.

## Question Quality Rules

- **Be specific**: Not "What about permissions?" but "Should providers see this feature, or only merchants with admin role?"
- **Include options**: Give concrete choices, not open-ended questions
- **Show consequences**: Each option should explain what it means for implementation
- **Suggest an answer**: Use Nash patterns and conventions to make your best guess
- **Prioritize ruthlessly**: P0 = literally cannot design without this. P1 = design will be suboptimal without it. P2 = polish detail.
- **Max 10 questions**: Force-rank. If you have more than 10, drop the lowest priority ones.

## After Completion

Tell the user:
- How many questions were generated and their priority breakdown
- Which questions can be answered by reading code (offer to do that)
- Suggested next step: "Answer the questions (or I can try to answer the code-answerable ones), then run `/nash-propose` for design proposals."
