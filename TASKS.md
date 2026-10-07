# SmartFA: Delegable Task Cards

Last updated: 2026-10-07

Each card is one unit of work for one sub-agent.

## Rules for every agent

1. **Only write to the paths in your card's `Owns` field.** If you need a change somewhere else, write it under "Requests for other cards" at the end of your output file.
2. Read `PLAN.md` §6 (non-negotiable constraints) before starting.
3. Cite sources for every fact. Mark uncertain items `(verify)`.
4. Finish by updating your card's status line in this file. That is the only edit to TASKS.md you may make.
5. Open one PR, or make one commit, per card. Commit message: `<CARD-ID>: <summary>`.

## Status key

`TODO` · `READY` (dependencies met) · `IN PROGRESS` · `REVIEW` · `DONE` · `BLOCKED`

---

## Agent brief template

Paste this when spawning a sub-agent:

```
You are working on SmartFA (repo root: <path>). Read README.md, PLAN.md §6, and
ARCHITECTURE.md first. Your task card is <CARD-ID> in TASKS.md — follow it exactly.
You may ONLY write to: <Owns paths>. Inputs to read: <Inputs>.
Done when: <Acceptance>. Cite every factual claim with URL + access date.
When finished, set the card status to REVIEW and commit as "<CARD-ID>: <summary>".
```

---

## Phase 1: Research (all can run in parallel now)

Full questions for each card are in [RESEARCH.md](RESEARCH.md). Agent type: `general-purpose`, with web access.

| ID | Card | Tier | Owns | Status |
|---|---|---|---|---|
| R00 | Legal & regulatory + G1 decision memo | P0 | `research/R00-*.md` | READY |
| R01 | Landscape & international models | P0 | `research/R01-landscape.md` | READY |
| R10 | Data sourcing, legality, freshness (G2) | P0 | `research/R10-data-sourcing.md` | READY |
| R02 | Health insurance & long-term care | P1 | `research/R02-health.md` | READY |
| R03 | Life, CI, disability, accident | P1 | `research/R03-protection.md` | READY |
| R09 | Needs-analysis methodology | P1 | `research/R09-methodology.md` | READY |
| R11 | How to apply / buying direct | P1 | `research/R11-application-guides.md` | READY |
| R12 | User pain points & mis-selling evidence | P1 | `research/R12-user-research.md` | READY |
| R04 | Savings, endowments, ILPs | P2 | `research/R04-savings-ilp.md` | READY |
| R05 | CPF & retirement | P2 | `research/R05-cpf-retirement.md` | READY |
| R06 | Investments | P2 | `research/R06-investments.md` | READY |
| R07 | Estate planning | P2 | `research/R07-estate.md` | READY |
| R08 | Tax | P2 | `research/R08-tax.md` | READY |
| R13 | Business model & independence | P3 | `research/R13-business-model.md` | READY |

**Review step:** R-RV (a fact-check agent, run after each R card) spot-checks 10 claims per research file against the cited sources. It appends its findings to a `## Review` section in that file.

---

## Phase 2: Design specs

Agent type: `Plan` for drafting, then a human sign-off (gate G3).

### D01: Data schema (final)
- **Depends:** R10 (G2), R02, R03
- **Owns:** `engine/src/schema/` (Zod schemas), `data/README.md`
- **Inputs:** ARCHITECTURE.md §3, research files
- **Acceptance:** Zod schemas cover Provider, Product (with per-category feature sub-schemas for every M1 category), PremiumTable, Scheme/Constant and Provenance. Includes one valid example JSON per category. `scripts/validate` (stub) can import the schemas.
- **Status:** TODO

### D02: Questionnaire spec
- **Depends:** R09, R12, G1
- **Owns:** `specs/questionnaire.md`
- **Acceptance:** each question has an ID, wording, type, validation, a "why we ask" line, skip logic and the rules that use it. M1 is completable in 10 minutes or less. No question is asked that no rule uses.
- **Status:** TODO

### D03: Rules spec
- **Depends:** R09, R02, R03, G1
- **Owns:** `specs/rules.md`
- **Acceptance:** a decision table for M1 with rule ID, condition, output, priority, rationale, source and constants used. "See a professional" triggers are listed. At least 20 persona cases have expected outputs worked by hand.
- **Status:** TODO

### D04: UX and information architecture
- **Depends:** R01, R12
- **Owns:** `specs/ux.md`
- **Acceptance:** sitemap, wireframes (ASCII or images) for home, questionnaire, results, compare, glossary term, wiki page and guide pages. Mobile-first. Must cover how the "Why?" trace and the "Last checked" freshness indicators are shown.
- **Status:** TODO

### D05: Compliance spec
- **Depends:** R00 (G1), R13
- **Owns:** `specs/compliance.md`
- **Acceptance:** disclaimer copy for each page type, an independence policy, a PDPA/privacy notice, the analytics policy, the list of "see a professional" triggers, and a launch checklist for gate G4.
- **Status:** TODO

---

## Phase 3: Build MVP (Module M1: Protection)

Agent type: `general-purpose`.

### B01: Site scaffold and CI *(no dependencies: can start now)*
- **Owns:** `site/`, `package.json` (workspace root), `.github/workflows/ci.yml`, `.github/workflows/deploy.yml`
- **Acceptance:** an Astro site builds and deploys from `main`. Content collections are wired to `../content/*` and `../data/*`. Pagefind is integrated. CI runs build, typecheck and tests. Includes a placeholder home page.
- **Status:** READY

### B02: Rules engine
- **Depends:** D01, D02, D03
- **Owns:** `engine/` (except `engine/src/schema/`, which is owned by D01)
- **Acceptance:** implements ARCHITECTURE.md §4. All persona fixtures from D03 pass. Every output number has a trace. Contains no network or DOM code. `FEATURE_RANKING` defaults to off.
- **Status:** TODO

### B03: Questionnaire UI
- **Depends:** B01, B02, D02, D04
- **Owns:** `site/src/components/questionnaire/`, `site/src/pages/plan/`
- **Acceptance:** shows progress, skip logic, back/edit and an optional save to `localStorage`. Keyboard and screen-reader accessible. Answers never leave the browser (verified in the network tab).
- **Status:** TODO

### B04: Results and comparison UI
- **Depends:** B02, B03, D04, D05
- **Owns:** `site/src/components/results/`, `site/src/pages/results/`, `site/src/pages/compare/`
- **Acceptance:** needs list with "Why?" expanders; a comparison table that is unranked and user-sortable; "Last checked" shown on every product; disclaimers from D05; export to JSON and print-to-PDF.
- **Status:** TODO

### B05: Glossary, wiki and guide templates
- **Depends:** B01, D04
- **Owns:** `site/src/layouts/`, `site/src/pages/glossary/`, `site/src/pages/wiki/`, `site/src/pages/guides/`
- **Acceptance:** term pages show related terms and the `fa_red_flag` callout. Glossary terms are auto-linked inside wiki and results text. Guide pages show the verified date on each link.
- **Status:** TODO

---

## Phase 3 (parallel): Content and data entry

Agent type: `general-purpose`. These cards are split by provider or topic so they can run in parallel.

### C01: Glossary terms
- **Depends:** R02, R03 (more terms are added as R04–R08 finish)
- **Owns:** `content/glossary/`
- **Acceptance:** at least 120 M1 terms, written in plain English with a reading level of grade 9 or below, each with sources. FA red-flag terms are marked (e.g. "guaranteed", "projected returns", "free rider").
- **Status:** TODO

### C02: Wiki: product-type explainers
- **Depends:** R02, R03
- **Owns:** `content/wiki/product-types/`
- **Acceptance:** one page per M1 category. Each covers what it is, who needs it, who doesn't, typical costs, common sales pitches and what to ask instead.
- **Status:** TODO

### C03: Wiki: provider profiles
- **Depends:** R03, R10
- **Owns:** `content/wiki/providers/`, `data/providers/`
- **Acceptance:** one profile per provider. Each covers products by channel, direct-purchase availability, claims/complaints contacts and provenance.
- **Status:** TODO

### C04-<provider>: How-to-apply guides
- **Depends:** R11, B05 (for frontmatter format)
- **Owns:** `content/guides/<provider>/` (one card per provider, run in parallel)
- **Acceptance:** one guide per product category the provider sells direct. Every link is verified with a date. Includes "switching safely" and "free-look" sections.
- **Status:** TODO

### C05-<provider>: Product data entry
- **Depends:** D01, R10 (pipeline decision)
- **Owns:** `data/products/<provider>/` (one card per provider, run in parallel)
- **Acceptance:** all current M1 products are entered and pass schema validation. Each has full provenance and an illustrative premium table for the standard profiles.
- **Status:** TODO

### C06: Government schemes and constants
- **Depends:** R02, R03, R05
- **Owns:** `data/schemes/`, `data/constants/`
- **Acceptance:** MediShield Life, CareShield Life, DPS, HPS and all CPF constants the engine needs. Each is dated with `effective_from`.
- **Status:** TODO

---

## Phase 4: Data pipeline

### P01: Validation and refresh scripts
- **Depends:** D01, R10
- **Owns:** `scripts/`
- **Acceptance:** `validate` checks every JSON file against the schemas. `refresh/<provider>` fetches and extracts data and writes a diff. `linkcheck` and `staleness` produce reports. All are runnable locally.
- **Status:** TODO

### P02: Scheduled workflows
- **Depends:** P01, B01
- **Owns:** `.github/workflows/refresh.yml`, `.github/workflows/linkcheck.yml`
- **Acceptance:** a weekly cron opens a review PR for data changes and an issue for broken or stale links. It never auto-merges.
- **Status:** TODO

### P03: Public changelog
- **Depends:** B01, P01
- **Owns:** `site/src/pages/changelog/`
- **Acceptance:** generated from the git history of `data/` at build time.
- **Status:** TODO

---

## Phase 4 (parallel): QA

### Q01: Persona review
- **Depends:** B02
- **Owns:** `engine/tests/personas/REVIEW.md`
- **Acceptance:** at least 20 personas run. Any output a fee-only planner would disagree with is flagged with reasoning. Ideally a human fee-only adviser does a final pass.

### Q02: Accessibility and mobile
- **Depends:** B03, B04, B05
- **Owns:** `specs/qa-a11y.md` (findings only; fixes go back to the owning B card)
- **Acceptance:** Lighthouse accessibility ≥ 95 on all page types, a keyboard-only questionnaire run, and checks at 360px width.

### Q03: Content fact-check
- **Depends:** C01–C06
- **Owns:** `specs/qa-factcheck.md`
- **Acceptance:** a random 10% sample of claims is checked against their sources, and the error rate is reported. Errors go back to the owning C card.

---

## Phase 5: Later modules

Repeat the D → B → C pattern for each module, reusing the engine and site:

- **M2 CPF & Retirement:** inputs R05, R08 · cards D03-M2, B02-M2, C01-M2, C06 update
- **M3 Savings & Investments:** inputs R04, R06, R08 · the G1 branch decides how far M3 can go
- **M4 Estate:** inputs R07 · mostly C02/C04-style guides

## Suggested first parallel batch

These cards have no unmet dependencies:

1. **R00 + R01 + R10:** these unblock gates G1 and G2.
2. **R02, R03, R09, R11, R12:** these unblock M1 design.
3. **B01:** the site scaffold, which needs no research.
