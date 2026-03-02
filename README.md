# eval-framework 🧪 — 3-tier classification eval with golden test sets and CI safety checks.

<p align="center">
  <img src="assets/hero.png" alt="eval-framework hero" width="1100">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white" alt="Node.js">
  <img src="https://img.shields.io/badge/Claude-191919?style=for-the-badge&logo=anthropic&logoColor=white" alt="Claude">
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL">
</p>

A classification evaluation framework that validates LLM outputs against golden test sets. Three evaluation tiers: unit tests for individual classifications, integration tests for pipeline consistency, and regression tests against historical golden sets. Runs in CI to prevent classification quality degradation before it reaches production.

> **Note:** This is a closed-source project. The README documents the architecture and learnings.

## What it does

- 3-tier evaluation: unit (single classification), integration (pipeline), regression (golden sets)
- Golden test sets: manually curated ground-truth examples for each job category
- CI integration: blocks deploy if classification accuracy drops below threshold
- Tracks accuracy, precision, recall, and F1 per category over time
- Compares model versions side-by-side (e.g., Claude 3.5 vs Claude 4)
- Generates confusion matrices and error analysis reports

## How it works

```
CI triggers eval suite
    |
    v
Load golden test set (curated ground-truth examples)
    |
    v
Run classification pipeline on test inputs
    |
    v
Compare outputs to ground truth
    |
    v
Calculate metrics per category (accuracy, precision, recall, F1)
    |
    +-- accuracy >= threshold --> deploy proceeds
    |
    +-- accuracy < threshold --> deploy blocked, failures reported
    |
    v
Store results (timestamp, model version, full metrics) for trend analysis
```

Historical tracking enables trend analysis across model versions and prompt changes. Each eval run is stored with its full context for reproducibility.

## Tech stack

- **Runtime:** Node.js test runner
- **AI:** Claude (classification model under test)
- **Database:** PostgreSQL for golden sets and historical results
- **CI:** GitHub Actions (eval job in deploy pipeline)
- **Reporting:** Markdown reports + Slack notifications

## What I learned

- Golden test sets need to be small and curated, not large and auto-generated — 50 hand-picked examples beat 500 scraped ones
- The biggest classification failures weren't model errors but prompt drift — prompts that worked in v1 degraded when the system around them changed
- CI safety checks on LLM outputs feel obvious in retrospect but almost nobody does it — most teams ship prompt changes without any regression testing
