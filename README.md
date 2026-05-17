# ProjectMage

Feature ideation and lifecycle planning grounded in company and domain context. Ideate features, deep-dive on the best ones, plan launches, and define success metrics — all from a single skill.

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
| `--count` | any number | `5` |
| `--audience` | e.g. `enterprise`, `new users` | all users |
| `--constraint` | e.g. `"no new tables"` | none |

**Modes:**
- `grounded` — shippable within a quarter, low ambiguity
- `wild` — novel mechanics, unexpected angles, push beyond obvious
- `moonshot` — big bets, paradigm shifts, long-horizon thinking

---

## Lifecycle Modes

Once an idea is worth pursuing, run it through the rest of the product lifecycle:

### Expand — deep-dive on one idea
```
projectmage expand: Plain-Language Credit Explainer
```
Produces: one-line pitch, MVP definition, milestones, risks & open questions, success metrics, stakeholder-ready paragraph.

The idea name comes from a previous ideation run's saved output. You don't need to get it exactly right — the agent fuzzy-matches your input against all saved idea headings (partial words, synonyms, and paraphrases all work). If it finds one clear match it proceeds automatically; if there are multiple plausible matches it asks you to confirm; if nothing matches it shows you the full list of saved ideas.

### Launch — go-to-market brief
```
projectmage launch: Campaign Attribution Scorecard
```
Produces: target audience, rollout strategy (alpha → limited GA → full GA), positioning, in-product entry points, instrumentation requirements, launch risks, internal comms brief.

### Measure — success metrics framework
```
projectmage measure: Attribution Health Score
```
Produces: north star metric, primary metrics table, counter-metrics, instrumentation events, measurement timeline, decision criteria (ship / iterate / kill).

---

## Manage Contexts

```
projectmage switch: notion-search     ← change active context
projectmage list                      ← see all saved contexts
```

Context files live in `references/` as `{company}-{domain}-context.md`. The active context is tracked in `references/active.txt`. Copy `references/company-context.template.md` to create a new one manually.

---

## Output

Every run saves a Markdown file to:

```
~/.hermes/output/projectmage/{company-slug}/{YYYY-MM-DD}-{mode}-{feature-area}.md
```

See [PLAN.md](PLAN.md) for architecture and implementation phases.

