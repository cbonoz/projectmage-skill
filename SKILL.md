---
name: projectmage
title: ProjectMage — Product Feature Lifecycle
description: "Use when user says 'projectmage' or wants to ideate, expand, launch, or measure product features for a company."
version: 1.1.0
platforms: [linux, macos, windows]
tags: [product, ideation, features, strategy, brainstorming, launch, metrics]
category: productivity
---

# ProjectMage: Product Feature Lifecycle

## When to Use

Trigger this skill when the user:
- Starts a message with `projectmage` (any casing)
- Asks to ideate, brainstorm, expand, launch-plan, or define success metrics for product features
- Uses commands like `projectmage save:`, `projectmage switch:`, `projectmage list`, `projectmage expand:`, `projectmage launch:`, or `projectmage measure:`
- Wants product strategy grounded in a specific company and feature domain context

**Don't use for:** general brainstorming unrelated to a specific company's product, or one-off creative ideation with no company context.

## Overview

ProjectMage covers the full product feature lifecycle — from raw ideation through deep-dive scoping, go-to-market planning, and success measurement — all grounded in stored company and feature domain context.

## Commands

| Command | What it does |
|---|---|
| `projectmage: {input}` | Ideate features for a problem area or feature slug |
| `projectmage expand: {idea name}` | Deep-dive one idea: milestones, risks, MVP, pitch |
| `projectmage launch: {idea name}` | Draft a go-to-market brief for a feature |
| `projectmage measure: {idea name}` | Define success metrics and a measurement framework |
| `projectmage save: {Company} — {Domain}` | Create or update a context profile |
| `projectmage switch: {slug}` | Change the active context |
| `projectmage list` | Show all saved contexts |

## Inputs

The user provides two things:

1. **Active context** — loaded automatically from `references/active.txt`. Must be set before first use via `projectmage save:` or `projectmage switch:`.
2. **Feature area or problem statement** — either a short slug (`search`, `onboarding`) or a natural language problem statement (`"attribution doesn't recommend what to do next"`). Both are valid. Problem statements will be interpreted into a focused feature area before ideation.

Optional modifiers:
- `--count N` — how many ideas to generate (default: 3)
- `--mode [wild|grounded|moonshot]` — creativity dial (default: `grounded`)
  - `grounded`: realistic, shippable within a quarter
  - `wild`: stretch ideas, novel mechanics, unexpected angles
  - `moonshot`: paradigm shifts, big bets, long-horizon thinking
- `--audience [segment]` — target a specific user segment (e.g., `power users`, `enterprise`, `new users`)
- `--constraint [text]` — a hard constraint every idea must satisfy (e.g., `no backend changes`, `must work offline`)

## Context Format

Context is scoped to a **company + feature domain pair**, not just a company. A context file covers one domain within one company (e.g. HubSpot × Attribution Reporting).

Naming convention: `references/{company}-{domain}-context.md`
Example: `references/hubspot-attribution-context.md`

A well-formed context block includes:

```
Company: <name>
Description: <what the overall product does>
Feature Domain: <the specific area being ideated on, e.g. Attribution Reporting>
Domain scope: <what this domain owns and what problems it solves>
Target users: <who uses this feature area and why>
Existing capabilities: <bullet list of what already exists in this domain>
Known pain points: <what users complain about or what's missing>
Competing approaches: <alternatives and how this product differs>
Goals: <what the domain is trying to achieve>
```

If context is missing or sparse, ask the user to fill it in before proceeding.

## Workflow

### Step 1 — Load context

Read `references/active.txt` to get the active context slug (e.g. `hubspot-attribution`).
- If `active.txt` is missing or empty: **stop immediately.** Ask the user: `"No active context set. What company and feature domain are you working on?"` Collect their answer, then run the `save` flow (Step 1a) before proceeding.
- Load `references/{slug}-context.md` (e.g. `references/hubspot-attribution-context.md`)
- If the file doesn't exist: **stop immediately.** Tell the user the context file is missing and ask them to describe the company and feature domain so it can be created. Run the `save` flow (Step 1a) before proceeding.

**Do not attempt to ideate without a loaded context. Never guess or proceed on empty context.**

#### Step 1a — Save flow (when context is missing or triggered by `projectmage save:`)

The `save:` command format is: `projectmage save: {Company} — {Feature Domain}` (e.g. `projectmage save: Acme Inc — attribution reporting`).

1. If the user provided `save: {Company} — {Domain}`, extract both from the command. Otherwise ask: `"What company and feature domain are you working on? (e.g. Acme Inc — Attribution Reporting)"`
2. If the company is well-known, infer a draft context from training knowledge and show it to the user for confirmation
3. Ask the user to correct or add anything (especially pain points and goals, which are hard to infer)
4. Write the confirmed context to `references/{company}-{domain}-context.md`
5. Write the slug to `references/active.txt`
6. Confirm: `"Context saved and activated: {Company} — {Domain}. Now continuing with your request..."`
7. Resume from Step 2

### Step 2 — Parse input

The user's input after `projectmage:` is either:
- **A slug** (`search`, `attribution windows`) — use directly as the feature area
- **A problem statement** (`"attribution doesn't recommend what to do next"`) — interpret it: extract the underlying feature area the problem points to, state it explicitly (e.g. `Feature area: next-step recommendations in attribution`), and confirm it in one line before proceeding

Then extract any modifiers:
- `count` (default: 3)
- `mode` (default: `grounded`)
- `audience` (optional)
- `constraint` (optional)

### Step 3 — Anchor to the product

Before ideating, synthesize 2–3 sentences that describe the product's core value proposition and what makes it unique. This grounds all ideas in the product's identity.

### Step 4 — Ideate

Generate `count` feature ideas for the specified `feature_area`. For each idea, produce:

```
## [Feature Name]

**What it does:** One sentence describing the feature.

**Why it matters:** How this solves a real user need or advances the company's goals.

**How it works (sketch):** 2–4 bullets on the mechanics — what the user sees/does.

**Effort estimate:** [Small / Medium / Large]

**Audience fit:** Who benefits most from this.
```

Apply the `mode` dial:
- `grounded`: ideas should be executable by a small team, low ambiguity, clear value
- `wild`: ideas may require novel UX, partnerships, or non-obvious approaches — push beyond the obvious
- `moonshot`: treat constraints loosely, aim for category-defining bets

If `--audience` is set, all ideas should be tailored to that user segment.
If `--constraint` is set, every idea must satisfy it — call it out explicitly.

### Step 5 — Synthesize

After the ideas, add a short **"What ties these together"** section (2–4 sentences) describing the strategic thread connecting the best ideas and how they'd reinforce the product's position.

### Step 6 — Save output

After generating ideas and the strategic thread, always save the full output to a file:

- Path: `output/projectmage/{company-slug}/{YYYY-MM-DD}-{feature-area-slug}.md` (relative to the agent's working output directory)
- Create the directory if it doesn't exist
- File header should include: company name, feature domain, feature area, mode, date, and any modifiers used
- Content: the full ideation output exactly as shown to the user
- Confirm to the user with the path where the file was saved

Example path: `output/projectmage/hubspot/2026-05-17-attribution-explainability.md`

### Step 7 — Optionally deep-dive (or run a lifecycle mode)

If the user invokes a lifecycle mode on a specific idea, run the appropriate workflow below instead of ideation.

---

## Lifecycle Mode: `expand`

**Invocation:** `projectmage expand: {idea name}`

### Idea Resolution

Before running, locate the idea's content:
1. Search `~/.hermes/output/projectmage/{company-slug}/` for a file containing an H2 heading matching `{idea name}` (case-insensitive, partial match is fine)
2. If found: load the full idea block (What it does, Why it matters, How it works) as context for the deep-dive
3. If not found: ask the user — `"I couldn't find '{idea name}' in your saved outputs. Can you paste the idea description or tell me which run it came from?"`
4. If multiple files match: list them and ask which one to use

Load context (Step 1). Then produce:

```
## {Idea Name} — Deep Dive

**One-line pitch:** A single sentence that sells the idea to an engineering lead.

**Problem it solves:** 2–3 sentences grounding it in a real user pain from the loaded context.

**MVP definition:** What is the smallest version worth shipping? What gets cut to get there?

**Milestones:**
1. {milestone} — {what's done / what's validated}
2. ...

**Risks & open questions:**
- {risk or question}
- ...

**Success metrics:** How will you know this worked? (see `measure` mode for full framework)

**1-paragraph pitch:** A stakeholder-ready paragraph suitable for a product review or roadmap doc.
```

Save to `~/.hermes/output/projectmage/{company-slug}/{YYYY-MM-DD}-expand-{idea-slug}.md`.

---

## Lifecycle Mode: `launch`

**Invocation:** `projectmage launch: {idea name}`

### Idea Resolution

Same as `expand` — search saved output files for the idea by H2 heading. Load the idea block before producing the brief.

Load context (Step 1). Then produce a go-to-market brief:

```
## {Idea Name} — Launch Brief

**Feature summary:** One sentence.

**Target audience:** Who is this for first? Which user segment gets the most value?

**Rollout strategy:**
- Phase 1 (alpha/beta): who, how many, what you're testing
- Phase 2 (limited GA): criteria to expand
- Phase 3 (full GA): criteria for full rollout

**Positioning:** How is this described to users? One headline + one subheadline.

**In-product entry points:** Where does the user discover this feature?

**Documentation & education:** What needs to be written (help articles, tooltips, onboarding)?

**Instrumentation:** What events must be tracked before launch?

**Launch risks:** What could go wrong at rollout?

**Internal comms:** What do sales, support, and CS need to know before it ships?
```

Save to `~/.hermes/output/projectmage/{company-slug}/{YYYY-MM-DD}-launch-{idea-slug}.md`.

---

## Lifecycle Mode: `measure`

**Invocation:** `projectmage measure: {idea name}`

### Idea Resolution

Same as `expand` — search saved output files for the idea by H2 heading. Load the idea block before producing the measurement framework.

Load context (Step 1). Then produce a measurement framework:

```
## {Idea Name} — Measurement Framework

**North star metric:** The single number that best captures whether this feature is working.

**Primary metrics:**
| Metric | What it measures | Target / threshold |
|---|---|---|
| ... | ... | ... |

**Counter-metrics:** What could go up that would mean the feature is backfiring?

**Instrumentation required:**
- Event: `{event_name}` — triggered when {action}
- ...

**Measurement timeline:**
- Week 1–2: baseline and early signal
- Week 4: first meaningful read
- Week 8–12: decision point (iterate, expand, or kill)

**Qualitative signals:** What user research or feedback channels will supplement the data?

**Decision criteria:** What result means "ship to everyone"? What means "iterate"? What means "kill"?
```

Save to `~/.hermes/output/projectmage/{company-slug}/{YYYY-MM-DD}-measure-{idea-slug}.md`.

## Example Invocations

```
# Context setup
projectmage save: Acme Inc — attribution reporting
projectmage switch: notion-search

# Ideation
projectmage: search
projectmage: "attribution doesn't recommend what to do next"
projectmage: onboarding --mode wild --count 8
projectmage: collaboration --audience enterprise --constraint "no new database tables"

# Lifecycle modes
projectmage expand: Plain-Language Credit Explainer
projectmage launch: Campaign Attribution Scorecard
projectmage measure: Attribution Health Score
```

## Output Format

Output is Markdown. Each idea is an H2 section. The synthesis is a final H2 section titled **"Strategic Thread"**.

If a context file was used, note it at the top: `> Context loaded from references/company-context.md`
If context was provided inline, confirm it was understood with a one-line summary before the ideas.
