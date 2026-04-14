---
name: position
description: Research a company or technology product and produce a position paper with buy/wait/no-buy recommendation. Analyzes value proposition, market landscape, competitive moat, financials, risks, and feature comparison. Use when evaluating tools, platforms, or vendors.
user_invocable: true
---

# Position Paper Researcher

Research a company or technology product and produce a structured position paper with a buy/wait/no-buy recommendation.

## Usage

```
/position Coder                                                                 # Research by company name
/position "HashiCorp Vault"                                                     # Quoted product name
/position Datadog --focus security                                              # With focus area
/position Coder --save                                                          # Save output to docs/position-papers/
/position Datadog --context "5k-person fintech, SOC2+PCI, AWS-only, Okta SSO"   # Tailored to enterprise shape
```

## Input Parsing

Parse the argument ($ARGUMENTS) to extract:
- **Subject**: Company or product name (required). If quoted, treat as exact name.
- **--focus <area>**: Optional focus area to emphasize (e.g., security, cost, developer-experience, AI)
- **--context "<description>"**: Optional enterprise context to tailor the evaluation. Describe the buyer: size, industry, regulatory posture (SOC2, HIPAA, PCI, FedRAMP, GDPR), deployment constraints (cloud-only, hybrid, air-gapped), critical integrations (Okta, Splunk, ServiceNow, etc.), and any hard requirements. When provided, weight moat/risk/fit analysis toward this shape — e.g., penalize SaaS-only tools for air-gapped buyers, reward strong SSO/SCIM for regulated buyers.
- **--save**: Save the final paper to `docs/position-papers/{subject-slug}-{year}.md`

If no subject is provided, ask the user what they'd like to research. If `--context` is omitted, the paper is written for a generic enterprise audience.

## Workflow

Execute three parallel research tracks, then synthesize into a single position paper.

### Phase 1: Parallel Research (use 3 subagents concurrently)

Launch all three research agents in a SINGLE message to maximize parallelism. Each agent should use WebSearch and WebFetch extensively.

#### Agent 1: Product & Company Research
Search for and gather:
- What the product/company does — core offering, value proposition, how it works
- Key features — capabilities, integrations, platform support
- Target audience — who buys this, which industries, company sizes
- Pricing model — free tier, open source, enterprise tiers, how they monetize
- Company info — founding date, HQ, leadership, headcount, funding history
- Recent news — major announcements, partnerships, product launches in the last 12 months
- **Integration & compatibility** — SSO support (SAML, OIDC) and which IdPs, SCIM provisioning, audit log export, SIEM integration, API surface (REST/GraphQL/SDKs), supported clouds/regions, data residency options
- **Exit & data portability** — data export formats, open vs proprietary APIs, migration tooling, contract termination clauses, how locked-in customer data becomes

#### Agent 2: Market Landscape & Competitors
Search for and gather:
- Market category definition — what analyst firms call this space
- Market size and growth — TAM, CAGR, adoption rates if available
- Top competitors — identify 3-5 main players in the same category
- Feature comparison — compare the subject against the top 1-2 competitors across: infrastructure flexibility, security/compliance, pricing, open source vs proprietary, enterprise readiness, integrations
- Analyst coverage — Gartner, Forrester, IDC mentions or reports on this category
- Market trends — what forces are reshaping this space (AI, regulation, consolidation, etc.)

#### Agent 3: Financials, Moat & Risk Analysis
Search for and gather:
- Funding history — all known rounds, investors, valuations
- Revenue/growth signals — ARR, customer count, growth rates (public or estimated)
- Key customers — notable enterprise logos, case studies
- Competitive moat assessment — what defensibility does the company have? Evaluate:
  - Network effects or ecosystem lock-in
  - Switching costs (technical, organizational)
  - Open source community as distribution moat
  - Regulatory/compliance advantages
  - Proprietary technology or data advantages
- Risks — competitive threats, market timing, execution risks, financial sustainability
- Crunchbase/Pitchbook-style data — investors, valuation, employee count
- **TCO components** — licensing model (per seat, usage-based, flat), published list prices or typical deal sizes, implementation/professional services costs, ops headcount needed to run, training/rollout cost signals
- **Procurement & legal signals** — MSA availability, DPA/BAA support, SLA terms, support tiers and response times, on-prem or VPC deployment option, certifications (SOC2, ISO 27001, HIPAA, FedRAMP, PCI)

### Phase 2: Synthesis

Combine all research into the output format below. Apply judgment to form a recommendation:

**BUY** — Strong product-market fit, defensible position, clear value for the buyer, acceptable risk profile
**WAIT** — Promising but immature, unclear differentiation, or better to pilot before committing
**NO BUY** — Weak moat, high risk, better alternatives exist, or poor fit for enterprise needs

If `--focus` was specified, weight the recommendation toward that dimension.

If `--context` was specified, tailor the analysis to that enterprise shape:
- Reframe benefits and risks against the stated constraints (regulatory, deployment, integration, scale).
- Explicitly call out hard-requirement misses (e.g., "no SAML in base tier — blocker for SSO-mandated orgs").
- Weight the recommendation toward fit for the stated context, not generic fit.
- Add a short "Fit for your context" paragraph near the top of the Recommendation section.

## Output Format

```markdown
# {Company/Product} — Position Paper: {Category}

**Date:** {today} | **Category:** {market category} | **Recommendation:** {BUY|WAIT|NO BUY} ({brief qualifier})

---

## Context
[2-3 sentences: why this category matters, what's driving evaluation]

## What {Company} Does
[Core product description, value proposition, how it works]
- **{Value pillar 1}** — description
- **{Value pillar 2}** — description
- **{Value pillar 3}** — description

## Company Snapshot

| Field | Detail |
|-------|--------|
| Founded | {year, location} |
| CEO | {name, background} |
| Total funding | {amount across N rounds} |
| Latest round | {amount, date, lead investor} |
| Revenue (est.) | {ARR or revenue if available} |
| Growth | {growth metrics if available} |
| Net dollar retention | {NDR if available} |
| Key customers | {notable logos} |

## Benefits
1. **{Benefit 1}** — explanation
2. **{Benefit 2}** — explanation
3. **{Benefit 3}** — explanation
[4-6 benefits total]

## Risks

| Risk | Severity | Mitigation |
|------|----------|------------|
| **{Risk 1}** | {High/Medium/Low} | {How it's mitigated or why it's acceptable} |
[4-6 risks total]

## Competitive Moat Assessment
[Narrative assessment of defensibility — what makes this company hard to displace]
1. **{Moat layer 1}** — explanation
2. **{Moat layer 2}** — explanation
[3-5 moat layers]

## Feature Comparison: {Subject} vs. {Top Competitor}

| Dimension | {Subject} | {Competitor} |
|-----------|-----------|--------------|
| **{Dimension 1}** | {detail} | {detail} |
[8-10 comparison dimensions]

**Other notable players:** [1-2 sentence summary of other competitors worth knowing about]

## Total Cost of Ownership

| Cost component | Year 1 | Year 3 | Notes |
|----------------|--------|--------|-------|
| **License / subscription** | {estimate} | {estimate} | {pricing model, tier assumptions} |
| **Implementation / migration** | {estimate} | {estimate} | {services, data migration, integration work} |
| **Operations (run-cost)** | {estimate} | {estimate} | {FTE headcount, infra, support tier} |
| **Training & change mgmt** | {estimate} | {estimate} | {rollout, enablement} |
| **Total (rough)** | {sum} | {sum} | |

[Mark estimates clearly when pricing is opaque. Omit this section entirely if there is no usable public pricing signal and no reasonable way to estimate.]

## Integration Checklist

| Requirement | Supported | Notes |
|-------------|-----------|-------|
| **SSO (SAML / OIDC)** | {Yes/No/Tier} | {IdPs supported, which plan tier} |
| **SCIM user provisioning** | {Yes/No/Tier} | |
| **Audit log export / SIEM** | {Yes/No/Tier} | {formats, destinations — Splunk, Datadog, S3, etc.} |
| **API coverage** | {Good/Partial/Thin} | {REST/GraphQL, rate limits, SDKs} |
| **Data residency** | {Regions} | {US / EU / other; customer-selectable?} |
| **Deployment options** | {SaaS / Self-host / Hybrid / Air-gapped} | |
| **Compliance certifications** | {list} | {SOC 2 Type II, ISO 27001, HIPAA, FedRAMP, PCI DSS} |

## Exit & Data Portability

- **Data export:** [what you can get out, in what formats]
- **API standards:** [open specs vs proprietary; terraform provider?]
- **Migration path:** [tools, services, typical complexity to leave]
- **Contract terms:** [termination clauses, data-return SLAs if known]
- **Lock-in risk:** {Low / Medium / High} — [one-line narrative]

## Market Context
- [3-5 bullet points on market dynamics, analyst coverage, consolidation signals, trends]

## Recommendation: {BUY|WAIT|NO BUY}

**Rationale:**
- [4-6 bullets supporting the recommendation]

**Timing caveat:** [When/why the recommendation might change — what would flip it]

**Suggested next step:** [Concrete action the reader should take]
```

## Guidelines

- **Tone**: Analytical, direct, opinionated. This is a recommendation, not a survey.
- **Length**: 800-1200 words without context; may extend to ~1500 when `--context` is provided.
- **Honesty**: If data is thin (private company, no analyst coverage), say so. Don't fabricate metrics.
- **Tables over prose**: Use tables for structured comparisons. Use prose for narrative analysis (moat, recommendation).
- **Omit empty sections**: If no funding data exists, skip the funding rows rather than writing "N/A" repeatedly. Same for TCO when pricing is fully opaque.
- **Date awareness**: Use the current date. Search for recent (last 12 months) information to keep findings current.
- **Context-aware evaluation**: When `--context` is provided, every section should pull weight against those constraints. A BUY for a generic enterprise may be a NO BUY for a SOC2+HIPAA healthcare org on GCP — say so plainly.

## Error Handling

- If WebSearch is unavailable: Tell the user "Web search is required for this skill. Please ensure WebSearch is enabled."
- If very little information is found (pre-revenue startup, stealth mode): Produce a shorter "preliminary assessment" format and note the data limitations clearly.
- If the subject is ambiguous (e.g., "Mercury" could be multiple companies): Ask the user to clarify with a URL or description.
