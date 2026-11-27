# Evaluation

## Setup

| Item | Value |
|---|---|
| Model | Gemini 3.5 Flash |
| Battery | 3 simulated students (A1, B1, C1), 2 days, 10 turns a day: 60 turns |
| Rules | Deterministic checks on every reply |
| Secondary signal | LLM judge, same model family, scored 1 to 4 |

Targets were fixed before looking at results: at least 90% of turns without a failed rule, at most 5% failures on any single rule, median tutor latency up to 3 s, p95 up to 4 s, at most 2% of turns lost to infrastructure errors.

## Final configuration, four runs

| Measure | Run 1 | Run 2 | Run 3 | Run 4 | Mean | Target |
|---|---|---|---|---|---|---|
| Turns without failure | 96.7% | 90.0% | 91.7% | 88.3% | **91.7%** | ≥ 90% |
| Median tutor latency | 2.8 s | 2.9 s | 2.9 s | 2.6 s | **2.8 s** | ≤ 3 s |
| p95 tutor latency | 7.2 s | 6.9 s | 7.9 s | 8.9 s | **7.7 s** | ≤ 4 s, missed |
| Reply length over limit | 0% | 5% | 5% | 6.7% | **4.2%** | ≤ 5% |
| Judge score | 3.8 | 3.5 | 3.5 | 3.5 | **3.6** | none |

Run 4 includes two later fixes to spoken text; they did not move the aggregate beyond noise. By level: A1 88%, B1 96%, C1 91%. The pass-rate target was met in three of four runs.

## What changed the numbers

| Change | Effect |
|---|---|
| Tell the model the reply is spoken | Unspeakable markup: 16.7% → 0% |
| Retry and filter narrated tool calls | Turns lost to errors: 0 |
| Fix a three-sentence example inside the prompt | B1 length failures: 18% → 1.8% |
| Remove ambiguity in the A1 limit | Rewrites: 26.7% → 8.3% |
| Minimal reasoning on the tutor | Median latency: 4.5 s → 2.5 s, about 65% fewer tokens |
| Temperature 0.4 | Less run-to-run swing |
| Minimal reasoning on the evaluator | 57% fewer tokens, grade within noise |

Rejected: a smaller model for the tutor (median 1.3 s, but 25% of replies over the length limit and a judge score of 3.0), and a prompt rule against one-word exclamations (pass rate fell to 72.9%).

Latencies above are for the tutor alone. After the length check moved into code, the whole turn had a median of 2.7 s over 36 turns, 34 of them within 4.9 s.

These measurements show rule adherence with simulated students. They are not evidence of learning outcomes.
