# SmartFA: Research Agenda

Last updated: 2026-10-07

There are 14 research cards. Each one is self-contained, so it can go to a separate sub-agent.

## How to run a research card

1. Copy `research/_TEMPLATE.md` to the card's output path.
2. Answer every question under "Questions". If one can't be answered, write `UNKNOWN` and say what you tried.
3. **Cite every factual claim** with a URL and the date you accessed it. Use primary sources (MAS, MOH, CPF Board, IRAS, insurer sites, legislation at sso.agc.gov.sg) over blogs. Blogs are fine for leads, but don't use them as the final source.
4. Mark anything time-sensitive (premiums, caps, scheme amounts, rule changes) with `as of YYYY-MM-DD`.
5. End with **"Implications for SmartFA"**: concrete design consequences, written as bullet points.
6. The card is done when its "Done when" line is true.

> **Verify everything below.** The background notes in each card come from the planning pass and are leads, not facts. Facts already known to need checking are marked **(verify)**.

## Priority tiers

| Tier | Cards | Why |
|---|---|---|
| **P0: blocks gates** | R00, R01, R10 | Decide gates G1 (legal framing) and G2 (data sourcing) |
| **P1: blocks MVP (M1 Protection)** | R02, R03, R09, R11, R12 | Needed to design the questionnaire, rules and content for M1 |
| **P2: blocks later modules** | R04, R05, R06, R07, R08 | M2–M5 |
| **P3: strategy** | R13 | Business model and independence |

All P0 and P1 cards can run in parallel right now.

---

## R00: Legal and regulatory framework *(P0, decides G1)*

**Output:** `research/R00-legal.md` + `research/R00-decision-memo.md`

**Questions**
1. Under the **Financial Advisers Act (FAA)**, what exactly counts as a "financial advisory service"? Is a website that (a) explains product types, (b) computes coverage needs, (c) shows an unranked comparison, or (d) ranks named products for a user regulated? Draw the line between each.
2. What do the **MAS Guidelines on Provision of Digital Advisory Services** (robo-advisers, 2018) require? Does an insurance-needs calculator fall under them, or only investment-product advice?
3. Which **exemptions** exist? Examples: publishers of general information, newspapers/periodicals, execution-only platforms, and exempt FAs. What conditions apply to each?
4. How do **compareFIRST** (run by MAS, LIA and CPF/MoneySense) and **Direct Purchase Insurance (DPI)** work legally? Who operates them? What disclaimers do they carry? What is the current DPI sum-assured cap? It was $400k per insurer **(verify)**.
5. What do the **MAS Fair Dealing Guidelines**, **FAIR review reforms**, **Balanced Scorecard framework** and **commission disclosure / FA remuneration rules** say? These define the problem SmartFA is solving and explain why FA incentives look the way they do.
6. What do the **Insurance Act** rules on advertising and comparisons of insurance products say? Are there rules against "misleading comparisons"?
7. **PDPA:** if no data leaves the browser, which obligations still apply? Consider analytics, contact forms and optional result export.
8. What liability does a free information site carry for an incorrect needs calculation? Which disclaimers are standard (compareFIRST, MoneySense, MoneySmart, Seedly)?
9. **MAS FinTech Regulatory Sandbox / Sandbox Express:** is it a route to Branch B? What does it cost and how long does it take?
10. What licence does a **fee-only adviser partner** (Branch C) need? Which fee-only firms exist in Singapore?

**Sources:** sso.agc.gov.sg (FAA, Financial Advisers Regulations, Insurance Act); mas.gov.sg (guidelines, notices, FAQs, consultation papers); compareFIRST and MoneySense terms pages; law firm briefings (Rajah & Tann, Allen & Gledhill, Drew & Napier) on robo-advice.

**Done when:** the decision memo picks Branch A, B or C from PLAN.md §4 and lists the exact disclaimer text needed. It also lists the open questions that need a real lawyer's sign-off before launch.

---

## R01: Existing landscape and gap analysis *(P0)*

**Output:** `research/R01-landscape.md`

**Questions**
1. **compareFIRST:** which product types does it cover? What can it compare, and where does it fall short (UX, coverage, freshness, no needs analysis)? Can its data be used or linked?
2. **MoneySense** (Basic Financial Planning Guide, Financial Planning Check), **CPF planners** and **MOH IP comparison tables:** what government tools already exist?
3. **Commercial sites:** MoneySmart, SingSaver, Seedly, Dollars & Sense, Smart Wealth, The Money Bees and others. How do they make money (affiliate or lead-gen)? How does that bias their rankings?
4. **Fee-only and low-conflict advisers in Singapore:** for example Providend, plus whatever happened to MoneyOwl **(verify current status)**. What do they charge?
5. **International models.** How do people buy without a commissioned agent, and what made that possible?
   - **UK:** Retail Distribution Review (2013 ban on adviser commission for investments), MoneyHelper, comparison sites, execution-only.
   - **Australia:** FoFA reforms, Hayne Royal Commission (2019), Moneysmart.gov.au, and the life insurance commission caps.
   - **Netherlands:** the 2013 commission ban on complex products.
   - **US:** Policygenius, fiduciary vs suitability standards, online term life.
   - **India:** SEBI fee-only RIA regime, insurance aggregator rules.
6. Which features from those models could transfer to Singapore, and which depend on regulation that Singapore lacks?

**Done when:** a feature × competitor matrix exists, plus a "SmartFA's unique value" section of 3–5 bullets with evidence for each.

---

## R02: Health insurance and long-term care *(P1, M1)*

**Output:** `research/R02-health.md`

**Questions**
1. **MediShield Life:** coverage, claim limits, deductibles, co-insurance, premiums by age, MediSave usage, and recent or upcoming changes.
2. **Integrated Shield Plans (IPs):** all insurers. Currently 7: AIA, Great Eastern, HSBC Life, Income, Prudential, Raffles Health Insurance, Singlife **(verify)**. For each insurer, list:
   - plan tiers (private / A / B1)
   - premiums by age band, split into MediSave-payable and cash
   - pro-ration factors, pre/post hospitalisation days, panel vs non-panel rules
   - pre-authorisation, claim limits and lifetime limits
3. **Riders:** the 2026 rider reform. Riders sold from 1 Apr 2026 reportedly no longer cover the deductible and have a higher co-payment cap **(verify all details)**. Map old vs new rider structures per insurer.
4. **CareShield Life**, ElderShield (legacy) and the private **supplements**: payout amounts and premiums per insurer.
5. **Pre-existing conditions, underwriting and portability:** what happens when switching IPs? (Switching is a classic FA trap.)
6. Where does MOH/CPF publish official IP comparison tables, and how often are they updated?

**Done when:** a premium table template is filled in for at least 3 age bands × 7 insurers × 3 tiers, with sources, and the switching risks are documented.

---

## R03: Life, critical illness, disability and accident *(P1, M1)*

**Output:** `research/R03-protection.md`

**Questions**
1. **Product types:** term (level/decreasing/renewable/convertible), whole life, universal life, endowment-with-protection, DPS, HPS, critical illness (early/multi-pay/standalone vs accelerated), disability income, TPD, personal accident, hospital cash. Define each, list typical use cases and the typical *mis-selling* pattern for each.
2. **Government schemes:** Dependants' Protection Scheme (DPS) and Home Protection Scheme (HPS). Coverage amounts, premiums, opt-out rules.
3. **Providers** (verify which are active): AIA, Great Eastern, Prudential, Income, Singlife, Manulife, HSBC Life, Tokio Marine, FWD, China Taiping, Etiqa, Raffles. For each, list current term, whole life and CI products, and whether each is sold via DPI, direct online, agency only or bancassurance only.
4. **Premium benchmarks:** term vs whole life for 3 standard profiles (age, gender, smoker status), using compareFIRST and provider quote tools. Calculate the "buy term and invest the difference" comparison with stated assumptions.
5. **The 2023 LIA critical illness definitions** (standardised definitions for 37 conditions?) **(verify)**.
6. **Riders:** waiver of premium, early CI, and so on. Which are worth having and which are padding?

**Done when:** a product-type reference table and a provider × product matrix exist, plus 3 benchmark premium comparisons with sources.

---

## R04: Savings, endowments and investment-linked policies *(P2, M3)*

**Output:** `research/R04-savings-ilp.md`

**Questions**
1. How do ILPs work in Singapore? Cover fee layers (insurance charges, fund fees, supplementary charges), bonus/loyalty mechanics, surrender charges and typical break-even years.
2. Endowments: guaranteed vs non-guaranteed returns, the illustrated rates (4.25% / 3% projections **(verify)**), and surrender value curves.
3. What MAS/LIA rules apply to benefit illustrations and product summaries?
4. Why do FAs push these products? Look at commission structure by product type.
5. Alternatives with lower fees: SSB, T-bills, fixed deposits, CPF top-ups, ETFs.

**Done when:** a worked example compares an ILP against buy-term-and-invest over 20 years, with fees itemised and sources cited.

---

## R05: CPF and retirement *(P2, M2)*

**Output:** `research/R05-cpf-retirement.md`

**Questions**
1. CPF account structure (OA/SA/MA/RA), interest rates, contribution rates by age, and the current BRS/FRS/ERS figures. Include the 2025 SA closure at 55 **(verify)** and any later changes.
2. CPF LIFE plans (Standard/Basic/Escalating), payout estimates, and how to choose between them.
3. Top-up schemes (RSTU, MRSS), tax relief caps, and SRS (contribution caps, withdrawal rules, tax treatment).
4. Using CPF for housing: accrued interest, and the trade-off with retirement adequacy.
5. Which official CPF calculators exist? Can SmartFA link to them or reproduce their logic?
6. Support schemes: Silver Support, Workfare.

**Done when:** every number the engine needs for M2 is listed in a table, with its source and the date it was last changed.

---

## R06: Investments *(P2, M3, highest licensing risk)*

**Output:** `research/R06-investments.md`

**Questions**
1. Low-risk options: SSB, T-bills, MAS bills, fixed deposits, high-yield savings accounts. How to buy each one directly.
2. Brokers and platforms: fees, CDP vs custodian accounts, and the platforms available to retail investors.
3. Robo-advisers (Endowus, StashAway, Syfe, and others): fees, CPF/SRS eligibility, and their licensing status.
4. CPFIS: what is allowed, and the historical performance data.
5. What is the **line between education and advice** for investments under the FAA/SFA? (Cross-reference R00.)

**Done when:** an options table exists covering risk, liquidity, fees, minimums and the official link for each option. A recommendation on what M3 can legally output is also included.

---

## R07: Estate planning *(P2, M4)*

**Output:** `research/R07-estate.md`

**Questions**
1. Wills (Wills Act requirements, cost options, DIY validity), intestacy rules (Intestate Succession Act), and Muslim estates (Faraid / Inheritance Certificate).
2. CPF nomination: how it works, and the fact that CPF is not covered by a will.
3. Insurance nominations: Insurance Act **s49L (trust) vs s49M (revocable)** **(verify section numbers)**, and when to use each.
4. LPA (Office of the Public Guardian) and AMD: forms, fees, process.

**Done when:** each item has a step-by-step guide with official links and costs.

---

## R08: Tax *(P2, M5)*

**Output:** `research/R08-tax.md`

**Questions:** personal income tax reliefs connected to financial planning (CPF cash top-up, SRS, life insurance relief, the overall relief cap), with amounts and conditions, all from IRAS.

**Done when:** a relief table exists with caps and IRAS links.

---

## R09: Needs-analysis methodology and algorithm design *(P1, core IP)*

**Output:** `research/R09-methodology.md`

**Questions**
1. **Published Singapore guidelines:** the MoneySense Basic Financial Planning Guide (e.g. death/TPD cover ≈ 9× annual income, CI ≈ 4× **(verify)**), the LIA Protection Gap Study, and the emergency fund rule of thumb.
2. **Needs-analysis frameworks:** DIME, Human Life Value, capital needs analysis, income replacement. Write out the formulas and the assumptions each one needs.
3. **Inputs needed:** age, dependants and their ages, income, debts and mortgage, existing coverage (including employer group insurance), health/smoker status, assets, CPF balances, risk tolerance, budget. **Keep it minimal**, since every question costs completion rate.
4. **Prioritisation logic** when the budget is limited. A typical order: health → death/TPD for anyone with dependants → disability income → CI → savings. Find a published basis for whichever order is chosen.
5. **Affordability rules:** for example, total protection premiums ≤ 10–15% of take-home pay **(verify the source)**.
6. **Risk profiling** for M3: standard questionnaires such as MAS/robo-adviser practice and FinaMetrica-style instruments.
7. **Explainability:** how should each recommendation trace back to the rule and inputs that produced it? Look at prior art in rule engines (json-rules-engine, decision tables) and at how output is displayed in regulated robo-advisers.
8. **Edge cases that should trigger "see a professional":** pre-existing conditions, business owners, foreigners/non-PRs, high net worth, special-needs dependants, divorce.

**Done when:** a draft rule list exists (rule ID, condition, output, rationale, source) covering M1, plus the minimal question list with a justification for each question.

---

## R10: Data sourcing, legality and freshness *(P0, decides G2)*

**Output:** `research/R10-data-sourcing.md`

**Questions**
1. For each provider, where does product data live? Cover product pages, product summary PDFs, policy wording PDFs, quote tools and compareFIRST. Which formats are machine-readable?
2. What do the **terms of use and robots.txt** say for each insurer site, compareFIRST, MOH and CPF? Is scraping allowed? Can facts be republished? (Facts aren't copyrightable, but brochures are.)
3. Does compareFIRST or any aggregator offer a **data licence, API or download**?
4. **Change frequency:** how often do premiums, products and IP tables change? What events trigger changes (MOH annual reviews, insurer repricing)? Use this to set per-field staleness thresholds.
5. **LLM-assisted extraction:** how reliable is extracting fields from product summary PDFs? Design the human review step.
6. **Link stability:** how often do insurer URLs break? What archiving strategy should be used (e.g. Wayback snapshots of source PDFs)?

**Done when:** a sourcing matrix (provider × source × allowed? × format × change frequency) exists, plus a recommended pipeline for gate G2.

---

## R11: How to apply: buying direct *(P1, M1)*

**Output:** `research/R11-application-guides.md`

**Questions**
1. For each provider and product type: can it be bought online without an agent? What is the URL? Which documents are needed? Does it support Singpass/MyInfo? What medical underwriting applies, and how long does it take?
2. **DPI** purchase flow at each insurer.
3. What do **free-look periods** (14 days? **verify**), cancellation, replacement and switching rules involve? How do you safely replace an existing FA-sold policy (never cancel before the new cover is in force)?
4. How do you request a **policy review or surrender value** from an insurer without going through the original agent? How do you change the servicing agent or go "orphan"?
5. Complaints: insurer IDR, then **FIDReC**, then MAS. Document the process and link to each.

**Done when:** a guide skeleton exists per provider × product type, with verified URLs and a `last_verified` date.

---

## R12: User pain points and mis-selling evidence *(P1)*

**Output:** `research/R12-user-research.md`

**Questions**
1. Common mis-selling patterns, from r/singaporefi, HardwareZone, Seedly, news reports and **FIDReC annual reports** (complaint categories and statistics).
2. MAS enforcement actions against FAs and representatives (prohibition orders): what patterns show up?
3. Which questions do people actually ask when they try to buy without an FA? Where do they get stuck?
4. Which products are people most often over-sold? Which are most often under-bought?

**Done when:** there is a ranked list of the top 10 pain points, each with evidence, and each mapped to a SmartFA feature that addresses it.

---

## R13: Business model and independence *(P3)*

**Output:** `research/R13-business-model.md`

**Questions**
1. How can SmartFA be funded without recreating the conflict it exists to remove? Options: donations, grants (e.g. MAS FSDF, IMDA), paid premium tools, sponsorship from non-providers, and flat-fee advice via a partner.
2. If affiliate links are ever used: which disclosure rules apply, and what neutrality policy makes them safe (equal links for all providers, no effect on ranking)?
3. Running costs: hosting (near zero for a static site), data maintenance labour, and legal review.

**Done when:** a recommended model exists, plus a draft independence policy for the site footer.

---

## Research output template

See `research/_TEMPLATE.md`.
