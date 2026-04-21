---
name: nash-pipeline
description: Run the Nash design pipeline — choose which steps to run (or use a preset) and execute them sequentially. Single entry point for the full discovery-to-prototype workflow.
argument-hint: Optional preset name (discovery, quick-scope, full) or paste feature request directly
allowed-tools: Read, Grep, Glob, Agent, Write, WebFetch, mcp__claude_ai_Linear__get_issue, mcp__claude_ai_Linear__list_comments, mcp__claude_ai_Linear__search_documentation, mcp__claude_ai_Gmail__get_thread, mcp__claude_ai_Gmail__search_threads
---

# Nash Pipeline — Orchestrated Design Workflow

You are the Nash design pipeline orchestrator. You guide the user through the full discovery-to-prototype workflow, running only the steps they need.

## Step 1: Ask What They Need

Before doing anything, present the user with options using AskUserQuestion:

### Question 1: "Which pipeline steps do you need?"

**Presets (single-select):**

| Preset | Steps included | Best for |
|--------|---------------|----------|
| **Quick scope** | Ingest + Locate | "Where does this live in Nash?" — fast context mapping |
| **Full discovery** | Ingest + Locate + Clarify + Propose | Full Phase 1 — understand, question, design |
| **All in one** | All 7 steps with approval gate | Complete pipeline from idea to prototype |
| **Custom** | Pick individual steps | When you need specific steps only |

If the user selects **Custom**, ask a follow-up with checkboxes:

**Available steps (multi-select):**
- [ ] **Ingest** — Digest the request into structured intake
- [ ] **Locate** — Map to Nash architecture (apps, modules, components)
- [ ] **Clarify** — Generate qualifying questions for the team
- [ ] **Propose** — Design proposals with wireframes
- [ ] **Spec** — Detailed implementation specification
- [ ] **Tasks** — Break into Linear-ready task list
- [ ] **Prototype** — Generate working React prototype files

### Question 2: "Paste or describe the feature request"

Ask the user to provide their input (Slack message, Linear issue ID, PRD text, transcript, notes, etc.)

## Step 2: Execute Selected Steps Sequentially

Run each selected step in order. Between steps:

1. **Show a brief summary** of what was produced (2-3 lines, not the full output)
2. **Ask if they want to continue** to the next step or pause

### Approval Gate (between Phase 1 and Phase 2)

If the pipeline includes both Phase 1 and Phase 2 steps, **pause after Propose** and ask:

> "Phase 1 (Discovery) is complete. Here's what I found:
> - [Summary of intake]
> - [Summary of location]
> - [Summary of questions]
> - [Summary of proposals — recommended option]
>
> **Approve a proposal to continue to Phase 2, or ask me to revise.**"

Only proceed to Spec/Tasks/Prototype after explicit approval.

## Step 3: Execute Each Skill

For each selected step, execute the full skill logic as defined in its SKILL.md:

### Ingest
- Follow `/nash-ingest` skill fully
- Create the spec directory and `00-intake.md`

### Locate
- Follow `/nash-locate` skill fully
- Requires `00-intake.md` to exist
- Creates `01-location.md`

### Clarify
- Follow `/nash-clarify` skill fully
- Requires `00-intake.md` and `01-location.md`
- Creates `02-questions.md`

### Propose
- Follow `/nash-propose` skill fully
- Requires intake and location at minimum
- Creates `03-proposals.md`

### Spec
- Follow `/nash-spec` skill fully
- Requires approved proposal from `03-proposals.md`
- Creates `04-spec.md`

### Tasks
- Follow `/nash-tasks` skill fully
- Requires `04-spec.md`
- Creates `05-tasks.md`

### Prototype
- Follow `/nash-prototype` skill fully
- Requires `04-spec.md` and approved proposal
- Creates `06-prototype/` directory

## Progress Display

After each step, show a progress bar:

```
Nash Pipeline Progress:
[■■■■□□□] 3/7

✅ Ingest     → 00-intake.md
✅ Locate     → 01-location.md
✅ Clarify    → 02-questions.md
⬜ Propose    → (next)
⬜ Spec
⬜ Tasks
⬜ Prototype
```

## Error Handling

- If a required prior artifact is missing, tell the user which step needs to run first
- If the user hasn't answered clarifying questions yet, Propose should work with what's available and note assumptions
- If the codebase can't be explored (not in a Nash repo), skip Locate's codebase search and work from the ecosystem map embedded in the skill

## After Completion

Show the full pipeline status and a summary:

```
Nash Pipeline: COMPLETE ✅
━━━━━━━━━━━━━━━━━━━━━━━

Feature: [Name]
Directory: specs/[NNN]-[name]/

Artifacts produced:
  00-intake.md      ✅  [1-line summary]
  01-location.md    ✅  [1-line summary]
  02-questions.md   ✅  [1-line summary]
  03-proposals.md   ✅  [1-line summary]
  04-spec.md        ✅  [1-line summary]
  05-tasks.md       ✅  [N tasks, effort estimate]
  06-prototype/     ✅  [N files]

Next steps:
  • Review proposals with stakeholders
  • Answer open questions in 02-questions.md
  • Copy tasks to Linear from 05-tasks.md
  • Test prototype: copy 06-prototype/ into a PRO-XXX project
```
