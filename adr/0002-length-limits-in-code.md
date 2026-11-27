# ADR 2: Check reply length in code, not with a second model

**Status:** accepted, replaces a model-based compliance check

## Context

Beginner replies must be short. A sentence too many at A1 and the learner is lost. The first design had a second model call review each A1 and A2 draft before it was spoken. It added about 5 s (median) to a conversation where silence is very noticeable. Lowering its reasoning effort cut tokens by about 60%, and it stopped being able to count words: 5 of 8 A1 turns broke the limit.

## Decision

Sentence limits, word limits (with a tolerance of two words) and the A1 tense ceiling are plain code. A short opener such as "Awesome!" is dropped without calling the model again. Anything else sends the draft back to the tutor with the reason.

## Alternatives

- **Model reviewer with full reasoning.** Catches more kinds of problems, costs seconds on every turn.
- **Prompt only.** Free, but the lab showed the model exceeds limits often enough to matter.
- **Truncate the reply.** Instant, but it usually cuts the closing question, which keeps the conversation going.

## Consequences

- The check runs in microseconds. Whole-turn latency was 2.7 s median over 36 turns.
- It is testable without a model.
- The tense check is a heuristic. Vocabulary above the level passes.
- The model-based check remains behind a configuration flag for comparison.
