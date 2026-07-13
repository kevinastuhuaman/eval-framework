# Evaluation Policy for LLM Classifiers

A public architecture case study for deciding whether a model, prompt, rubric, or pipeline change is safe to promote.

[Portfolio](https://portfolio.kevinastuhuaman.com) | [Interactive evaluation control room](https://kevinastuhuaman.github.io/evals-control-room/) | [Enterprise AI interface kit](https://github.com/kevinastuhuaman/enterprise-ai-interface-kit)

The system behind this case study evaluates job-classification behavior across function, seniority, region, sponsorship, salary, and experience signals. The public artifact focuses on the reusable product contract, not Trackly's private prompts, labels, user data, or production implementation.

## The product problem

Aggregate accuracy can improve while an important slice becomes less reliable. A model can also win on quality while violating the feature's latency or cost budget. An evaluation system therefore needs to produce a release decision, not only a dashboard.

The decision should answer:

1. What changed, and which exact candidate is under review?
2. Which dataset and rubric define expected behavior?
3. Which failure slices matter more than the aggregate score?
4. What quality, cost, and latency constraints must all pass?
5. Who owns the promotion decision and its recorded rationale?

## Three evaluation layers

| Layer | Question | Typical evidence | Failure meaning |
| --- | --- | --- | --- |
| Contract | Does one classification obey its schema and rubric? | Fixed examples, required fields, allowed labels, refusal cases | The output cannot safely enter the pipeline |
| Pipeline | Does the complete workflow preserve inputs, transformations, and downstream behavior? | End-to-end fixtures, versioned dependencies, repeat runs | A component works alone but the product path is inconsistent |
| Regression | Is the candidate at least as reliable as the approved baseline on important slices? | Locked golden set, slice metrics, confusion matrix, changed-case review | Promotion could degrade behavior users rely on |

## Promotion decision flow

```text
Candidate model, prompt, rubric, or pipeline change
        |
        v
Versioned evaluation manifest
        |
        v
Contract checks and complete pipeline run
        |
        v
Compare against the approved baseline
        |
        +---- inspect aggregate quality
        +---- inspect high-risk slices
        +---- inspect changed cases and confusion pairs
        +---- inspect latency and cost budgets
        |
        v
Promote, hold for evidence, or reject with a recorded reason
```

## Minimum promotion contract

| Decision input | Required context |
| --- | --- |
| Candidate | Model, prompt, rubric, code, and dependency versions |
| Baseline | Last approved candidate and its evaluation manifest |
| Dataset | Immutable version, provenance, inclusion policy, and known coverage gaps |
| Metrics | Aggregate result plus feature-specific and high-risk slices |
| Changed cases | Inputs whose outcome changed, with expected and candidate labels |
| Budgets | Feature-level quality, cost, and end-to-end latency thresholds |
| Decision | Owner, timestamp, outcome, and evidence-backed rationale |

## Golden-set policy

A golden set is a governed product asset, not a bag of convenient examples.

- Include ordinary cases, boundary cases, known regressions, and abstention or unavailable states.
- Keep the expected label and rubric version reviewable by a human domain owner.
- Prevent candidate outputs from silently rewriting the expected answer.
- Record dataset changes separately from model or prompt changes so improvements are attributable.
- Review slice coverage before trusting a strong aggregate score.
- Treat production feedback as a candidate for adjudication, not automatic ground truth.

## Failure taxonomy

- **Schema failure:** missing or invalid output fields; stop before scoring downstream quality.
- **Rubric disagreement:** the candidate and expected label differ; route to changed-case review.
- **Slice regression:** aggregate quality passes but a consequential category falls below its gate.
- **Pipeline drift:** the same model behaves differently because preprocessing, context, or postprocessing changed.
- **Budget regression:** quality passes while latency or cost exceeds the feature contract.
- **Coverage gap:** the test set does not represent a newly important input; hold the decision until evidence exists.
- **Unstable result:** repeated runs vary beyond the tolerated boundary; do not promote on the best run.

## Human review loop

Human review has two distinct jobs:

1. Adjudicate whether the rubric or the candidate is wrong on disputed examples.
2. Own the release decision when evidence is incomplete or tradeoffs cross product boundaries.

The evaluation system can calculate and organize evidence. It should not silently redefine the truth set or authorize its own promotion.

## Interactive product proof

The [Evaluation Control Room](https://kevinastuhuaman.github.io/evals-control-room/) makes this decision model inspectable. A reviewer can compare a baseline and candidate, inspect regression cases and a confusion matrix, apply a correction, rerun the suite, and see the promotion gate change.

The control room uses fictional records and synthetic values. Its purpose is to demonstrate interaction and decision design; its displayed case count, thresholds, scores, cost, and latency are not Trackly production metrics.

## What this demonstrates

- Product judgment about model quality as a release decision rather than a single score.
- Technical fluency with versioned datasets, classification metrics, slice analysis, pipeline tests, and CI gates.
- A human-in-the-loop boundary for adjudication and promotion.
- Clear separation between real system experience, public architecture, and synthetic interface evidence.

## Public boundary

This repository contains no applicant or user data, private prompts, production labels, golden-set records, model credentials, internal endpoints, exact production thresholds, or proprietary Trackly implementation. See [`IP-NOTICE.md`](IP-NOTICE.md), [`llms.txt`](llms.txt), and [`project.json`](project.json).
