---
name: projectmage
title: ProjectMage — Company Feature Ideation
description: "Use when user says 'projectmage' or wants to brainstorm/ideate product features for a company."
version: 1.0.0
platforms: [linux, macos, windows]
tags: [product, ideation, features, strategy, brainstorming]
category: productivity
---

# ProjectMage: Company Feature Ideation

## When to Use

Trigger this skill when the user:
- Starts a message with `projectmage` (any casing)
- Asks to ideate, brainstorm, or generate feature ideas for a company or product
- Uses commands like `projectmage save:`, `projectmage switch:`, or `projectmage list`
- Wants product strategy or feature inspiration grounded in a specific company context

**Don't use for:** general brainstorming unrelated to a specific company's product, or one-off creative ideation with no company context.

## Overview

ProjectMage takes stored company context and a **feature area** parameter, then generates a structured set of actionable feature ideas grounded in the company's product, market, and constraints.

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

### Step 7 — Optionally deep-dive

If the user asks to expand on a specific idea, go deeper:
- Break it into milestones
- Identify risks and open questions
- Suggest success metrics
- Draft a 1-paragraph pitch for it

## Example Invocations

```
projectmage save: Acme Inc — attribution reporting
```

```
projectmage save: Notion — search
```

```
projectmage: search
projectmage: "attribution doesn't recommend what to do next"
projectmage: onboarding --mode wild --count 8
projectmage: collaboration --audience enterprise --constraint "no new database tables"
```

## Output Format

Output is Markdown. Each idea is an H2 section. The synthesis is a final H2 section titled **"Strategic Thread"**.

If a context file was used, note it at the top: `> Context loaded from references/company-context.md`
If context was provided inline, confirm it was understood with a one-line summary before the ideas.
