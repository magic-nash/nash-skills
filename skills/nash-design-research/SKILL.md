---
name: nash-design-research
description: Visual design-pattern research via Mobbin — generates search phrases from a feature description, searches Mobbin flows/screens/sections (web app vs mobile aware), and produces a file with links, pattern analysis, and guidance on which patterns fit Nash. Use when you need real screen/flow references before designing. Trigger: /nash-design-research <feature>
argument-hint: Feature or UI area to research (e.g. "dispatch exception queue", "mobile proof of delivery")
allowed-tools: Read, Grep, Glob, Write, Bash, ToolSearch, mcp__claude_ai_Mobbbin__search_flows, mcp__claude_ai_Mobbbin__search_screens, mcp__claude_ai_Mobbbin__search_sections
---

# Nash Design Research — Mobbin Pattern Research

You are a design researcher for Nash, a delivery orchestration platform. Given a feature or UI area, search Mobbin for real screens, flows, and sections from shipped products, then recommend which patterns fit Nash.

## Inputs

- A feature/UI description (argument)
- If `specs/<NNN>-<feature>/00-intake.md` and/or `02a-product-research.md` exist, read them first — reuse the problem frame and target user; don't re-ask.

## Execution Steps

### Step 1: Determine platform scope

Mobbin's `platform` parameter accepts only `web` or `ios` — use `ios` for all mobile research (Android is not available). Infer scope from the Nash target app, or ask only if genuinely ambiguous:
- **nash-portal / nash-provider / nash-store** → `web`
- **driver app** → `ios`
- **nash-tracking** → mobile-first web → search `web`, optionally also `ios` for native analogies
Pass the platform into every Mobbin query and say in the output which scope was used.

### Step 2: Generate search phrases

Create **4-8 Mobbin search phrases**, from three angles:
1. **Literal** — the feature's own vocabulary ("order tracking", "delivery status")
2. **Pattern-level** — the UI pattern underneath ("activity feed", "split view detail panel", "bulk actions table", "timeline", "map with list")
3. **Analogous domains** — industries with the same shape of problem ("support ticket inbox", "trading order book", "ride hailing driver app", "incident management")

List the phrases in the output file so the search is reproducible.

### Step 3: Search Mobbin

The Mobbin MCP tools are `mcp__claude_ai_Mobbbin__search_flows`, `mcp__claude_ai_Mobbbin__search_screens`, `mcp__claude_ai_Mobbbin__search_sections` (note: "Mobbbin" with triple b). If their schemas aren't loaded, load them first via ToolSearch (`select:mcp__claude_ai_Mobbbin__search_flows,...`).

- **search_flows** — for multi-step journeys (onboarding, checkout, triage→resolve)
- **search_screens** — for single-surface patterns (dashboard, detail view, queue)
- **search_sections** — for components within a screen (headers, cards, filters)

Run searches for each phrase, platform-scoped. Fire independent searches in parallel. Collect: app name, screen/flow title, and the Mobbin link for each relevant hit. Discard hits that are visually pretty but structurally irrelevant.

### Step 4: Analyze patterns

Group the hits into **named patterns** (e.g. "Inbox + detail split view", "Status timeline with hero header"). For each pattern:
- Which apps use it (2+ apps = established pattern)
- What problem it solves and when it breaks down
- **Fit for Nash**: map to Nash conventions — table→drawer→tabs mental model, dispatcher loop (awareness→triage→investigate→decide→commit→monitor), DataTable/Sheet/Dialog components, semantic tokens. Mark each pattern **Adopt / Adapt / Avoid** with one-line reasoning.

### Step 5: Write the output

**Location:**
- If a matching `specs/<NNN>-<feature>/` directory exists → write `02b-design-research.md` there
- Otherwise → `~/Documents/research/nash/<yyyy-mm-dd>-<slug>-design-research.md` (create the dir if needed)

### Step 6: Build the interactive HTML report

Alongside the markdown, always produce `02b-design-research.html` (same directory, plus an `assets/` subfolder):

1. **Download every referenced screen** with `curl -sL "<image_url>" -o assets/<app>-<n>.webp` — Mobbin `image_url`s expire after 30 days, so never hot-link them; embed the downloaded files with relative paths. Keep `mobbin_url` as the citation link on each image.
2. **Self-contained single HTML file** (inline CSS/JS, no external deps, no CDN). Structure:
   - Sticky header: feature name, platform scope, date, verdict filter chips (**Adopt / Adapt / Avoid** — clicking filters pattern sections)
   - One section per pattern: verdict badge, description, Nash-fit note, and a **horizontally scrollable gallery** (`overflow-x: auto`, scroll-snap) of the downloaded screens — big enough to read (min-height ~420px), click opens a lightbox (full-size image + app name + link to Mobbin), drag-to-scroll with mouse
   - **Wireframe proposals section**: for each relevant Nash app type (portal / provider / driver mobile / tracking), an ASCII wireframe in `<pre>` (monospace, box-drawing characters, same style as `/nash-propose`) sketching how the recommended pattern could look in that app, with 2-3 annotation lines under each
   - Footer: reference table (app / type / pattern / Mobbin link) and search phrases used
3. Keep it elegant but lightweight: system font stack, neutral palette, dark-mode friendly (`prefers-color-scheme`). No frameworks.

End with a 5-line chat summary: strongest pattern recommendation, 2-3 best Mobbin links, and the HTML file path (clickable).

## Output Format: `02b-design-research.md`

```markdown
# Design Research: [Feature]

**Date**: [date] · **Platform scope**: [web app / mobile / mobile-first web]
**Search phrases**: [list of phrases used]

## Patterns Found

### Pattern: [Name] — **Adopt / Adapt / Avoid**
- **Seen in**: [App A](mobbin-link), [App B](mobbin-link)
- **What it is**: [2-3 sentences, structure not aesthetics]
- **Solves**: [the user problem it addresses]
- **Breaks down when**: [limits]
- **Nash fit**: [mapping to Nash components/conventions; why Adopt/Adapt/Avoid]

[repeat per pattern]

## Reference Links
| App | Type | Pattern | Link |
|---|---|---|---|
| [app] | screen/flow/section | [pattern] | [url] |

## Guidance for /nash-propose
1. [Primary recommended pattern + why]
2. [Secondary/alternative]
3. [Anti-patterns to avoid and why]
```

## Rules

- Every pattern claim needs at least one Mobbin link; prefer 2+ apps per pattern.
- Describe structure (layout, hierarchy, interaction), not visual styling — Nash has its own tokens.
- Always run the three phrase angles; literal-only searches miss the best analogies.
- If Mobbin returns nothing useful for a phrase, say so in the file — a documented gap is a finding.
- Research-only: no wireframes, no component code — hand guidance to `/nash-propose`.
- Pairs with `/nash-research` (product/user side); this skill covers the visual/pattern side.
