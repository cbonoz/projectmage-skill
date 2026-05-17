# ProjectMage

Feature ideation grounded in company and domain context. Give it a problem area, get structured feature ideas back.

---

## Setup

First time for a new company/domain:

```
projectmage save: Acme Inc — attribution reporting
```

Company name comes first, then the feature domain. The agent infers context from its training knowledge, shows it to you, you correct or add anything, and it saves the profile and sets it active. After that, no setup needed.

---

## Ideate

```
projectmage: search
projectmage: "attribution doesn't recommend what to do next"
projectmage: onboarding --mode wild
projectmage: billing --count 8 --audience enterprise
projectmage: notifications --constraint "no backend changes"
```

Both short slugs and natural language problem statements work. Output is saved automatically after each run.

### Modifiers

| Flag | Options | Default |
|---|---|---|
| `--mode` | `grounded`, `wild`, `moonshot` | `grounded` |
| `--count` | any number | `3` |
| `--audience` | e.g. `enterprise`, `new users` | all users |
| `--constraint` | e.g. `"no new tables"` | none |

**Modes:**
- `grounded` — shippable within a quarter, low ambiguity
- `wild` — novel mechanics, unexpected angles, push beyond obvious
- `moonshot` — big bets, paradigm shifts, long-horizon thinking

---

## Manage Contexts

```
projectmage switch: notion-search     ← change active context
projectmage list                      ← see all saved contexts
```

Context files live in `references/` as `{company}-{domain}-context.md`. The active context is tracked in `references/active.txt`. Copy `references/company-context.template.md` to create a new one manually.

---

## Output

Every run saves a Markdown file. The default path is:

```
output/projectmage/{company-slug}/{YYYY-MM-DD}-{feature-area}.md
```

The output directory can be configured to match your environment (e.g. a local folder, a shared drive, or your agent's output path).

See [PLAN.md](PLAN.md) for architecture and implementation phases.

