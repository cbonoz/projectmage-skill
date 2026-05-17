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

Store company context as individual Markdown files in `references/`. A lightweight pointer file (`references/active.txt`) tracks which context is currently selected.

```
projectmage/
├── SKILL.md
├── README.md
├── PLAN.md
└── references/
    ├── active.txt                        ← contains e.g. "hubspot-attribution"
    ├── hubspot-attribution-context.md
    ├── notion-search-context.md
    └── company-context.template.md
```

`active.txt` contains a single line: the filename stem of the active context file.

### Why This Over Alternatives

| Option | Tradeoff |
|---|---|
| Single `company-context.md` | Simple but only one company at a time with no history |
| Inline context each time | Flexible but defeats the "simple invocation" goal |
| External DB / API | Too heavy for a file-based skill system |
| Named profiles + active pointer | Minimal friction, supports multi-company, fully readable/editable |

---

## Invocation Flow (Runtime)

When the user invokes `projectmage: {input}`:

1. Read `references/active.txt` → get `{slug}`
2. Read `references/{slug}-context.md` → load profile
3. If file missing or `active.txt` empty → run save flow inline (ask user for company + domain)
4. Interpret input as feature area slug or problem statement
5. Parse any modifiers (`--mode`, `--count`, `--audience`, `--constraint`)
6. Run ideation per `SKILL.md` workflow
7. Save output to `~/.hermes/output/projectmage/{company-slug}/{date}-{feature-area}.md`

---

## Implementation Phases

### Phase 1 — Core ideation with single profile ✅
`SKILL.md` handles this. Context loaded from `references/active.txt` pointer.

### Phase 2 — Multi-profile + active pointer ✅
Named `{company}-{domain}-context.md` files. `active.txt` as pointer. Inline save flow when context is missing.

### Phase 3 — `save` command with LLM-assisted parsing
- Add explicit `projectmage save:` invocation mode to `SKILL.md`
- Agent parses natural language → structured profile → writes file
- Optionally: web research step to auto-fill public company info

### Phase 4 — `switch` and `list` commands
- `projectmage list` — reads `references/`, prints available contexts, highlights active
- `projectmage switch: {slug}` — validates slug exists before writing pointer

### Phase 5 — Profile enrichment (optional)
- On `save`, optionally trigger web research to augment sparse profiles
- On ideation, optionally fetch recent company news to inject as context
