# SmartFA

**Plan your own finances in Singapore without a commission-paid financial adviser.**

Many Singapore financial advisers (FAs) are paid by commission. That pays them to sell products, not to fit products to your needs. SmartFA aims to replace that conflicted middleman with:

1. **A needs questionnaire + transparent rules engine.** You answer questions about your life and finances. It tells you what coverage/accounts you need, how much, and *why*. Every recommendation traces back to your answers and a published rule.
2. **A neutral comparison layer.** Products from major Singapore providers, compared side by side. No commissions, no sponsored ranking.
3. **A glossary + mini-wiki.** Plain-English explanations of every term an FA might use on you.
4. **How-to-apply guides.** Step-by-step instructions for buying direct, with links that are kept current.
5. **Freshness built in.** Every data point shows its source and the date it was last checked.

## Status

**Phase 0: planning (done).** No code yet. Research is the next step.

| File | What it is |
|---|---|
| [PLAN.md](PLAN.md) | Phases, gates, dependency graph, the open legal decision |
| [RESEARCH.md](RESEARCH.md) | The research agenda: 14 research cards, each with questions, sources and a done-when |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Stack, directory layout, data schemas, engine design, refresh pipeline |
| [TASKS.md](TASKS.md) | Delegable task cards for sub-agents, with dependencies and file ownership |

## Principles

- **Independent.** No commissions, no paid placement, no affiliate links that change rankings. See R13 in RESEARCH.md.
- **Explainable.** Rules are deterministic and published. No black-box scoring.
- **Sourced.** Every product fact has a `source_url` and `last_verified` date.
- **Private.** The questionnaire runs in your browser. Answers are never sent to a server.
- **Not advice (yet).** Whether SmartFA can legally name "the best policy for you" depends on Singapore's Financial Advisers Act. This is gate G1 in PLAN.md.
