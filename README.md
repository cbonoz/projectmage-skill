# ProjectMage — Implementation Plan

## Goal

Make ideation invocable with a single short phrase like:

> "projectmage: search"

No re-pasting of company context. No setup ritual each time.

---

## Memory Architecture

### The Problem

Every invocation needs rich company context (product description, users, features, pain points) but the user shouldn't have to re-supply it. The context also needs to support multiple companies (consultants, portfolio companies, side projects).

### Chosen Approach: Named Company Profiles + Active Pointer

Store company context as individual Markdown files in `references/companies/`. A lightweight pointer file (`references/active.txt`) tracks which company is currently selected.

```
projectmage/
├── SKILL.md
├── README.md
└── references/
    ├── active.txt                  ← contains e.g. "linear"
    └── companies/
        ├── linear.md
        ├── notion.md
        └── acme-corp.md
```

Each company file is a structured Markdown profile (see **Company Profile Format** below). `active.txt` contains a single line: the filename stem of the active profile.

### Why This Over Alternatives

| Option | Tradeoff |
|---|---|
| Single `company-context.md` | Simple but only one company at a time with no history |
| Inline context each time | Flexible but defeats the "simple invocation" goal |
| External DB / API | Too heavy for a file-based skill system |
| Named profiles + pointer | Minimal friction, supports multi-company, fully readable/editable |

---

## Commands

The skill supports four invocation modes:

### 1. `ideate` (default — just say the feature area)

```
projectmage: onboarding
projectmage: search --mode wild
projectmage: billing --count 8 --audience enterprise
```

The skill reads `references/active.txt`, loads the named profile, and runs ideation. No other input needed.

### 2. `save` — Create or update a company profile

```
projectmage save: [company name]
[paste or describe the company context]
```

The skill extracts the structured fields from what the user provides (via LLM parsing — no strict format required from the user) and writes or overwrites `references/companies/{slug}.md`. It then sets this company as active.

If the user only gives a company name (e.g., `projectmage save: Notion`), the skill should attempt to infer context from its training knowledge and ask the user to confirm or correct before saving.

### 3. `switch` — Change the active company

```
projectmage switch: notion
projectmage switch: linear
```

Updates `references/active.txt`. Lists available profiles if the name isn't found.

### 4. `list` — Show available profiles and active company

```
projectmage list
```

Reads `references/companies/` and prints a table of profiles with their one-line descriptions. Highlights the active one.

---

## Company Profile Format

Each `references/companies/{slug}.md` follows this schema:

```markdown
# {Company Name}

**Slug:** {slug}
**Last updated:** {date}

## Description
One to two sentences on what the product does and the core value proposition.

## Target Users
Who uses it. What job they're hiring the product for.

## Existing Features
- Feature A
- Feature B
- Feature C

## Known Pain Points
What users complain about or what gaps exist.

## Competitors
Key alternatives and how this product is differentiated.

## Growth Goals
What the company / team is trying to achieve next.

## Notes
Any free-form strategic context, recent launches, pivots, etc.
```

The LLM is responsible for parsing user input into this format during `save`. The schema is intentionally loose — partial profiles are valid and ideation will work with whatever is present, flagging missing fields as assumptions.

---

## Invocation Flow (Runtime)

When the user invokes `projectmage: {feature_area}`:

1. Read `references/active.txt` → get `{slug}`
2. Read `references/companies/{slug}.md` → load profile
3. If file missing or `active.txt` empty → prompt user to run `projectmage save:` or `projectmage switch:`
4. Parse any modifiers (`--mode`, `--count`, `--audience`, `--constraint`)
5. Run ideation per `SKILL.md` workflow

When the user invokes `projectmage save: {name}`:

1. Parse user-supplied context (loose natural language is fine)
2. Map to profile schema, filling gaps with reasonable inference
3. Write to `references/companies/{slug}.md`
4. Overwrite `references/active.txt` with `{slug}`
5. Confirm: `"Saved and activated: {Company Name}"`

---

## Ideal Conversational UX

First time (one-time setup per company):

```
User:  projectmage save: Linear
Agent: [infers from training knowledge, asks user to confirm]
User:  looks good, also add that we're focused on enterprise this year
Agent: Saved and activated: Linear
```

Every subsequent time:

```
User:  projectmage: notifications
Agent: [5 grounded feature ideas for Linear's notifications surface]

User:  projectmage: search --mode wild
Agent: [5 wild feature ideas for Linear's search surface]
```

Switching companies:

```
User:  projectmage switch: notion
Agent: Active company set to: Notion

User:  projectmage: onboarding
Agent: [ideas grounded in Notion's context]
```

---

## Implementation Phases

### Phase 1 — Core ideation with single profile ✅
`SKILL.md` already handles this via `references/company-context.md`.

### Phase 2 — Multi-profile + active pointer
- Migrate `company-context.md` template to `references/companies/` structure
- Add `active.txt` pointer file
- Update `SKILL.md` to read from active profile at runtime

### Phase 3 — `save` command with LLM-assisted parsing
- Add `save` invocation mode to `SKILL.md`
- Agent parses natural language → structured profile → writes file
- Optionally: web research step to auto-fill public company info

### Phase 4 — `switch` and `list` commands
- Minimal additions to `SKILL.md`
- `list` reads directory, formats table
- `switch` validates slug exists before writing pointer

### Phase 5 — Profile enrichment (optional)
- On `save`, optionally trigger web research to augment sparse profiles
- On ideation, optionally fetch recent company news to inject as context
