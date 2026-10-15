---
name: nash-research
description: Product-side research of similar patterns in existing web and mobile apps — who the user is, their needs, goals, jobs-to-be-done, and how competitors/analogous products solve the same problem. Use when starting discovery on a feature and you want market/UX context before proposing designs. Trigger: /nash-research <feature or problem>
argument-hint: Feature, problem, or domain to research (e.g. "dispatch triage queue", "order tracking page")
allowed-tools: Read, Grep, Glob, Write, WebFetch, WebSearch, mcp__parallel-search__web_search, mcp__parallel-search__web_fetch
---

# Nash Research — Product Pattern Research

You are a product researcher for Nash, a delivery orchestration platform. Given a feature, problem, or domain, research how existing web/mobile products solve it and who they solve it for. The output feeds `/nash-propose` with real-world grounding.

## Inputs

- A feature description, problem statement, or domain phrase (argument)
- If a `specs/<NNN>-<feature>/00-intake.md` exists for this feature, read it first — reuse its problem statement, target user, and business context instead of re-deriving them.

## Execution Steps

### Step 1: Frame the research

From the input, write down (internally):
- **Problem domain** (e.g. "real-time dispatch exception triage")
- **Platform scope**: web app / mobile app / both (infer from the Nash target app: portal & provider → web, driver → mobile, tracking → mobile-first web)
- **3-6 search phrases** mixing: domain terms, analogous industries (logistics, support ticketing, ops dashboards, fintech queues), and UX pattern names

### Step 2: Search

Use `mcp__parallel-search__web_search` (preferred; one call with multiple `search_queries`) or WebSearch as fallback. Look for:

1. **Analogous products** — apps solving the same job, even in other industries (e.g. incident management tools for triage queues, food-delivery trackers for tracking pages)
2. **Product teardowns / case studies** — articles explaining WHY a product made a UX choice
3. **User-side evidence** — reviews, forum threads, job stories revealing real needs and complaints

Fetch 2-4 of the most promising pages with `web_fetch`/WebFetch for depth. Do not fetch everything — excerpts usually suffice.

### Step 3: Synthesize per product

For each relevant product found (aim for 3-6), capture:
- What it is, platform (web/mobile), link
- **Who the user is** (role, context, frequency of use)
- **User needs & goals** the product serves (jobs-to-be-done phrasing: "when X, I want Y, so I can Z")
- **How it solves the problem** — the key screens/flows/patterns, in words
- What transfers to Nash and what doesn't (B2B dispatcher context vs consumer context, data density, real-time needs)

### Step 4: Extract cross-product insights

- Recurring patterns (what everyone does → table stakes)
- Divergent approaches (where products disagree → design decision for us)
- Gaps (needs no one serves well → opportunity)
- Vocabulary users/industry actually use (feed into Nash operational vocabulary)

### Step 5: Write the output

**Location:**
- If a matching `specs/<NNN>-<feature>/` directory exists → write `02a-product-research.md` there and update `_meta.json` if present
- Otherwise → write `~/Documents/research/nash/<yyyy-mm-dd>-<slug>-product-research.md` (create the dir if needed)

### Step 6: Build the interactive HTML report

Alongside the markdown, always produce `02a-product-research.html` in the same directory — a self-contained single HTML file (inline CSS/JS, no external deps, no CDN):

- Sticky header: feature name, platform scope, date
- **User portrait panel**: who / needs (JTBD) / goals / pains as scannable cards
- **Reference products**: one card per product in a horizontally scrollable row (scroll-snap, drag-to-scroll) — product name, platform tag, "how it solves it", transfers/doesn't-transfer split, link out
- **Insights table** with the Implication-for-Nash column highlighted
- **Wireframe proposals section**: for each relevant Nash app type (portal / provider / driver mobile / tracking), an ASCII wireframe in `<pre>` (monospace, box-drawing characters, `/nash-propose` style) sketching how the recommended direction could look in that app, with 2-3 annotation lines under each
- Footer: sources list
- Styling: system font stack, neutral palette, dark-mode friendly (`prefers-color-scheme`), no frameworks

End with a 5-8 line summary in chat: top insight, strongest reference product, the one design decision this research should influence, and the HTML file path (clickable).

## Output Format: `02a-product-research.md`

```markdown
# Product Research: [Feature/Problem]

**Date**: [date] · **Platform scope**: [web / mobile / both]
**Search phrases used**: [list]

## Problem Frame
[2-3 sentences: the job the user is hiring this feature for]

## Target User Portrait
- **Who**: [role, context]
- **Needs**: [bulleted, JTBD phrasing]
- **Goals / success criteria**: [what "done" feels like for them]
- **Pains with current solutions**: [from reviews/forums if found]

## Reference Products
### [Product name] ([web/mobile]) — [link]
- **User**: ...
- **How it solves it**: ...
- **Transfers to Nash**: ... / **Doesn't transfer**: ...

[repeat per product]

## Cross-Product Insights
| Insight | Evidence | Implication for Nash |
|---|---|---|

## Recommended Direction
[3-5 bullets: which patterns to adopt, which to avoid, open questions for /nash-clarify]

## Sources
- [Title](url) — one line on what it contributed
```

## Rules

- Every claim about a product must have a link. No invented screenshots or features.
- Separate **observation** (what the product does) from **interpretation** (why, and what it means for Nash).
- B2B dispatcher tools ≠ consumer apps: always note the context gap when transferring a pattern.
- This skill is research-only: no proposals, no wireframes — that's `/nash-propose`'s job.
- For visual pattern references (screens, flows, Mobbin links) use `/nash-design-research` — this skill covers the product/user side.
