# Position Paper Skill

A Claude Code plugin that researches a company or technology product and produces a structured enterprise position paper with a **BUY / WAIT / NO BUY** recommendation.

The skill runs three parallel research tracks (product & company, market & competitors, financials & moat) and synthesizes the output into a dense 800–1500 word paper covering value, risk, competitive moat, feature comparison, **TCO**, **integration checklist**, and **exit & data portability**.

When invoked with `--context`, the paper is tailored to your enterprise shape — size, regulatory posture, deployment constraints, critical integrations — so the recommendation reflects *your* fit, not a generic one.

---

## Install

This repo ships as a single-plugin Claude Code marketplace. Two steps:

```
/plugin marketplace add git@github.com:cloudglo/position-paper-skill.git
/plugin install position-paper-skill@cloudglo-plugins
```

The first command registers the marketplace catalog (clones the repo to `~/.claude/plugins/marketplaces/`). The second installs the plugin. Pull updates later with `/plugin marketplace update cloudglo-plugins`.

HTTPS also works if you have a GitHub credential helper configured:

```
/plugin marketplace add https://github.com/cloudglo/position-paper-skill.git
```

### Fallback: direct clone

If you want to skip the marketplace plumbing, clone directly into your plugins dir:

```
git clone git@github.com:cloudglo/position-paper-skill.git ~/.claude/plugins/position-paper-skill
```

Web search must be enabled in your Claude Code environment — the skill depends on it.

---

## Usage

```
/position Coder                                                                 # Research by company name
/position "HashiCorp Vault"                                                     # Quoted product name
/position Datadog --focus security                                              # Emphasize a dimension
/position Coder --save                                                          # Save to docs/position-papers/
/position Snowflake --context "3k-person healthcare SaaS, HIPAA+SOC2, multi-cloud, Okta SSO"
```

### Flags

| Flag | Purpose |
|------|---------|
| `--focus <area>` | Weight the analysis toward a dimension: `security`, `cost`, `developer-experience`, `AI`, etc. |
| `--context "<desc>"` | Describe the buyer: size, industry, regulatory posture, deployment constraints, critical integrations, hard requirements. The paper is tailored to this shape. |
| `--save` | Write the paper to `docs/position-papers/{subject-slug}-{year}.md` in the current repo. |

---

## What the paper contains

- **Context & product summary** — what it does, core value pillars
- **Company snapshot** — funding, leadership, customers, growth signals
- **Benefits & Risks** — ranked, with severity and mitigation
- **Competitive moat assessment** — layered defensibility analysis
- **Feature comparison** — subject vs. top competitor, 8–10 dimensions
- **Total cost of ownership** — license, implementation, operations, training (Y1 and Y3)
- **Integration checklist** — SSO, SCIM, audit log export, API coverage, data residency, deployment options, compliance certifications
- **Exit & data portability** — export formats, API standards, migration path, lock-in risk
- **Market context** — analyst coverage, category dynamics
- **Recommendation** — BUY / WAIT / NO BUY with rationale, timing caveat, and suggested next step

---

## Philosophy

- **Opinionated, not a survey.** The paper is a recommendation. The reader should know what you think and why.
- **Context beats generality.** A BUY for a generic enterprise is often a NO BUY for a regulated one. `--context` forces the skill to make that call.
- **Honest about thin data.** If the target is private and pre-revenue, the paper says so and produces a preliminary assessment rather than fabricating metrics.
- **Tables over prose** for structured comparisons; prose for moat and recommendation narrative.

---

## Structure

```
position-paper-skill/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── position-paper-researcher/
        ├── SKILL.md
        └── evals/
            └── evals.json
```
