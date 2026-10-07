# SmartFA: Architecture

Last updated: 2026-10-07. Status: **draft**. D01 finalises the schemas after research.

## 1. Stack

| Layer | Choice | Why |
|---|---|---|
| Site | **Astro** (static output) + TypeScript | Content collections fit glossary/wiki/guides in Markdown. Ships near-zero JS. Interactive islands (questionnaire) can use React or Svelte. |
| Data | **JSON files in `data/`**, validated by **Zod** schemas | Versioned in git, so every price change has a diff, an author and a date. No database to run. |
| Engine | **Pure TypeScript package in `engine/`** | Deterministic, unit-testable, runs in the browser. No network calls. |
| Search | **Pagefind** | Static full-text search over the wiki and glossary. |
| Hosting | **Cloudflare Pages** or **GitHub Pages** | Free, static, deploys on push to `main`. |
| Automation | **GitHub Actions** | Validation on PRs, weekly data refresh, link check, staleness report. |

No backend and no accounts in the MVP. User answers stay in the browser (see PLAN.md §6).

## 2. Directory layout

```
SmartFA/
├── README.md  PLAN.md  RESEARCH.md  ARCHITECTURE.md  TASKS.md
├── research/            # R-card outputs (Markdown). One file per card.
├── specs/               # D-card outputs: questionnaire, rules, UX, compliance specs
├── data/                # Source of truth for all facts (JSON, schema-validated)
│   ├── providers/       #   one file per provider: aia.json, great-eastern.json ...
│   ├── products/        #   one dir per provider: products/aia/<product-id>.json
│   ├── schemes/         #   government schemes: medishield-life.json, cpf-life.json ...
│   └── constants/       #   CPF rates, retirement sums, tax caps (dated)
├── content/             # Human-readable Markdown, rendered by the site
│   ├── glossary/        #   one file per term: term-insurance.md
│   ├── wiki/            #   product-type explainers, provider profiles, topics
│   └── guides/          #   how-to-apply guides: <provider>/<product-type>.md
├── engine/              # Rules engine (pure TS, no DOM)
│   ├── src/
│   │   ├── questions.ts #   question definitions (generated from specs/questionnaire.md)
│   │   ├── rules/       #   one file per module: protection.ts, cpf.ts ...
│   │   ├── needs.ts     #   profile → needs
│   │   ├── match.ts     #   needs → product filter (+ optional rank, Branch B flag)
│   │   └── explain.ts   #   trace builder
│   └── tests/personas/  #   golden persona fixtures + expected outputs
├── site/                # Astro app (reads ../data, ../content, imports ../engine)
├── scripts/             # validate, refresh, linkcheck, staleness report
└── .github/workflows/   # ci.yml, refresh.yml (cron), linkcheck.yml (cron)
```

**Ownership rule:** every directory and file belongs to exactly one task card (see TASKS.md). Parallel agents never write to the same path.

## 3. Data schemas (draft)

Every factual record carries this provenance block:

```ts
const Provenance = z.object({
  source_url: z.string().url(),
  source_type: z.enum(["provider_page", "product_summary_pdf", "policy_wording", "comparefirst", "gov", "other"]),
  retrieved_at: z.string().date(),       // when fetched
  last_verified: z.string().date(),      // when a human confirmed it
  verified_by: z.string(),               // github handle or "agent:<id>" + reviewer
  archive_url: z.string().url().optional() // Wayback/local snapshot
});
```

### Provider

```ts
{
  id: "aia",
  name: "AIA Singapore",
  type: "life_insurer" | "general_insurer" | "health_insurer" | "bank" | "robo" | "gov",
  website: url,
  direct_purchase_url?: url,
  customer_service: { phone?, email?, branch_url? },
  complaints_url: url,
  sells_via: ("dpi" | "direct_online" | "agency" | "bancassurance" | "fa_firm")[],
  provenance: Provenance
}
```

### Product

```ts
{
  id: "aia-healthshield-gold-max-a",
  provider_id: "aia",
  name: "AIA HealthShield Gold Max A",
  module: "protection" | "cpf" | "savings_investment" | "estate",
  category: "integrated_shield" | "ip_rider" | "term_life" | "whole_life" | "critical_illness"
          | "disability_income" | "personal_accident" | "careshield_supplement" | "endowment" | "ilp" | ...,
  status: "available" | "closed_to_new" | "withdrawn",
  channels: ("dpi" | "direct_online" | "agency" | "bancassurance")[],
  apply_url?: url,
  guide_slug?: "aia/integrated-shield",
  features: { /* category-specific, validated by a per-category sub-schema */ },
  // e.g. integrated_shield: { ward_tier, panel_only, pre_hosp_days, post_hosp_days, annual_limit, ... }
  // e.g. term_life: { min_entry_age, max_entry_age, max_cover_age, min_sum_assured, max_sum_assured, convertible, renewable, ... }
  exclusions_summary: string[],
  documents: { product_summary?: url, policy_wording?: url, brochure?: url },
  premiums?: PremiumTable,                // or premium_ref to a separate file if large
  provenance: Provenance,
  staleness_days: number                  // threshold before the UI flags it (from R10)
}
```

### PremiumTable

```ts
{
  basis: "annual" | "monthly",
  currency: "SGD",
  dims: ["age_band", "gender", "smoker", "sum_assured"],  // varies by category
  rows: [{ age_band: "31-35", gender: "M", smoker: false, sum_assured: 500000, premium: 412.0,
           medisave_payable?: 300, cash?: 112 }],
  illustrative: boolean,     // true = sample quote, not a full rate card
  provenance: Provenance
}
```

### Scheme / Constant

Government schemes (MediShield Life, CareShield Life, DPS, HPS, CPF LIFE) and dated constants (BRS/FRS/ERS, contribution rates, tax relief caps):

```ts
{ id: "cpf-frs", value: number, unit: "SGD", effective_from: date, effective_to?: date, provenance: Provenance }
```

The engine reads constants by `id` + date, never as hard-coded numbers.

### Glossary term (Markdown frontmatter)

```yaml
term: Co-insurance
slug: co-insurance
aliases: [coinsurance]
module: protection
short: The percentage of a claim you pay after the deductible.
related: [deductible, integrated-shield-plan, rider]
fa_red_flag: false          # true = term often used in misleading pitches; shows a warning callout
sources: [{ url, retrieved_at }]
last_verified: 2026-10-07
```

### Guide (Markdown frontmatter)

```yaml
title: How to buy an Integrated Shield Plan directly from AIA
provider: aia
product_category: integrated_shield
steps_verified: 2026-10-07
links: [{ label, url, last_checked }]   # link checker reads these
prerequisites: [Singpass, MediSave account]
```

## 4. Engine design

```
Answers (browser) ─▶ Profile (normalised + derived fields)
                       │
                       ▼
                 Rules (per module, ordered, pure functions)
                       │  each rule: { id, when(profile), then(profile) → Need[], rationale, source }
                       ▼
                 Need[]  { type, amount?, priority, reasons: RuleRef[] }
                       │
                       ▼
                 Match: filter data/products by need.type + eligibility (age, cover, channel)
                       │  Branch A: unranked, user-sortable (by premium, cover, provider)
                       │  Branch B: score() behind feature flag FEATURE_RANKING
                       ▼
                 Result { needs, gaps, matches, trace, warnings, see_a_professional[] }
```

- **Rules are data plus small functions.** Each rule has a stable ID (e.g. `PROT-LIFE-003`), a plain-English rationale and a source citation. These are rendered in the "Why?" panel.
- **Trace:** every number in the result links back to its inputs, its rule ID and the constants it used, with their dates.
- **Persona tests:** `engine/tests/personas/*.json` hold input/expected-output pairs. CI fails on any change to the output unless the fixture is updated in the same PR. That makes every rule change reviewable.
- **Versioning:** the engine exports `RULESET_VERSION`. Result exports include it, so a user can see which rules produced their plan.

## 5. Data refresh pipeline ("constantly updates")

```
weekly cron (GitHub Actions)
  └─ scripts/refresh/<provider>.ts   fetch pages/PDFs allowed by R10 → extract fields
       └─ compare with data/ → if changed: open PR "data: <provider> changes detected"
            - diff of changed fields + source snapshot links
            - label needs-review; NEVER auto-merge
  └─ scripts/linkcheck.ts           every url in data/ + content/ → issue for broken links
  └─ scripts/staleness.ts           records past staleness_days → issue + UI banner
```

- Human review is mandatory for data changes.
- The site shows "Last checked DATE" on every product card, and a warning when a record is stale.
- A public `/changelog` page is generated from git history of `data/`. This shows the site is current.

## 6. Privacy and compliance

- No server, so no answers are stored. Any analytics must be cookieless and aggregate only (e.g. Plausible or Cloudflare Web Analytics). Analytics never receive questionnaire answers.
- Result export is client-side only: JSON and print-to-PDF.
- `specs/compliance.md` (D05) holds the disclaimer copy, the independence policy and the "see a professional" triggers.
