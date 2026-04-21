---
name: nash-ingest
description: Digest raw feature requests (Slack, transcripts, PRDs, Linear issues, Figma links, notes) into a structured intake document. Use when someone asks you to build something and you need to understand the request.
argument-hint: Paste or describe the feature request, or provide a Linear issue ID
allowed-tools: Read, Grep, Glob, WebFetch, Agent, mcp__claude_ai_Linear__get_issue, mcp__claude_ai_Linear__list_comments, mcp__claude_ai_Linear__search_documentation, mcp__claude_ai_Gmail__get_thread, mcp__claude_ai_Gmail__search_threads, Write
---

# Nash Ingest — Digest Raw Input into Structured Intake

You are a product design assistant for Nash, a delivery orchestration platform. Your job is to take ANY raw input about a feature request and produce a clean, structured intake document.

## Input Sources You Handle

- Pasted Slack/Teams messages (messy, informal)
- Meeting transcripts or notes
- PRD text or bullet points
- Linear issue IDs (fetch via MCP: `mcp__claude_ai_Linear__get_issue`)
- Figma links (note them for `/nash-locate`)
- Gmail threads (fetch via MCP: `mcp__claude_ai_Gmail__get_thread`)
- User's own research notes or Claude Code conversation summaries
- Any combination of the above

## Execution Steps

### Step 1: Detect input type
Identify what kind of input was provided. If a Linear issue ID is given (e.g., `PRO-123`, `NASH-456`), fetch the full issue and its comments via Linear MCP. If a Gmail thread is referenced, fetch it. If a Figma link is provided, note it for the next skill.

### Step 2: Extract structured information
Parse the raw input and extract:

1. **Feature name**: Short, descriptive name (used for directory naming)
2. **Problem statement**: What problem is being solved? In 2-3 sentences.
3. **Who requested it**: Name + role (PM, engineer, stakeholder, customer)
4. **Target user**: Which Nash user type is affected?
   - Merchant (nash-portal user)
   - Provider/Fleet operator (nash-provider user)
   - Driver (mobile app user)
   - End customer (nash-tracking user)
   - Internal/Admin (nash-store user)
   - Multiple (list which)
5. **Business context**: Why is this needed now? What's the urgency?
6. **Desired outcome**: What should the user be able to do after this ships?
7. **Constraints mentioned**: Timeline, tech limitations, dependencies, budget
8. **Raw quotes**: Preserve exact wording from the requester that captures intent
9. **Figma links**: Any design references provided
10. **Related features**: Existing Nash features this connects to

### Step 3: Flag ambiguities
Mark anything unclear with `[UNCLEAR: description of what's missing]`. Common gaps:
- Scope not defined (is this a full feature or a quick fix?)
- Target user not specified
- No success criteria mentioned
- Edge cases not addressed
- No mention of existing behavior to preserve

### Step 4: Auto-detect target app
Based on the content, guess which Nash app(s) this targets:
- **nash-portal** — Merchant-facing: orders, deliveries, shipping, returns, fleet, settings, analytics
- **nash-provider** — Provider/fleet-facing: jobs, contracts, opportunities, payments
- **nash-tracking** — End-customer-facing: public order tracking
- **nash-store** — Store/commerce operations

### Step 5: Create the spec directory and files

1. Determine the next spec number by checking existing `specs/` directories
2. Create `specs/<NNN>-<feature-name>/`
3. Write `00-intake.md` with the structured output
4. Write `_meta.json` with pipeline state

## Output Format: `00-intake.md`

```markdown
# Feature Intake: [Feature Name]

**Date**: [today's date]
**Source**: [Slack / Linear / Transcript / PRD / Research / Mixed]
**Requested by**: [Name, Role]

---

## Problem Statement
[2-3 sentences describing the problem]

## Target User
[User type(s) and which Nash app]

## Business Context
[Why now? What's driving this?]

## Desired Outcome
[What the user should be able to do after this ships]

## Constraints
[Timeline, tech, dependencies — or "None mentioned"]

## Raw Quotes
> [Exact quotes from the requester that capture intent]
> [Preserve original wording, even if messy]

## Figma References
[Links, or "None provided"]

## Related Nash Features
[Existing features this connects to, or "To be determined by /nash-locate"]

## Ambiguities & Unknowns
- [UNCLEAR: ...]
- [UNCLEAR: ...]

## Auto-Detected Target
- **App**: [nash-portal / nash-provider / nash-tracking / nash-store]
- **Confidence**: [High / Medium / Low]
- **Reasoning**: [Why this app was chosen]
```

## Output Format: `_meta.json`

```json
{
  "feature": "<feature-name>",
  "number": "<NNN>",
  "created": "<ISO date>",
  "source_type": "<slack|linear|transcript|prd|research|mixed>",
  "target_app": "<nash-portal|nash-provider|nash-tracking|nash-store>",
  "pipeline": {
    "ingest": "complete",
    "locate": "pending",
    "clarify": "pending",
    "propose": "pending",
    "spec": "pending",
    "tasks": "pending",
    "prototype": "pending"
  }
}
```

## After Completion

Tell the user:
- Summary of what was captured
- Number of ambiguities flagged
- Suggested next step: "Run `/nash-locate` to map this to the Nash codebase, or `/nash-pipeline` to continue the full flow."
