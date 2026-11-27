# Design patterns in Lery AI

Each entry says what the pattern is, what the code does, and where my version departs from the original. Sources are in [references.md](references.md).

Lery AI is a **workflow**, not an agent, in the sense of Anthropic's [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents): models are called through predefined code paths. The model does not decide what happens next.

## Context engineering

Anthropic's [context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) article treats context as a finite resource. That framing shaped the orchestration service.

| Technique | In Lery | Note |
|---|---|---|
| Layered system prompt | persona → tutor style → learner snapshot → level rules → lesson | Ordered from stable to volatile. I have not measured prompt cache hits, so I claim no cost benefit. |
| Just-in-time retrieval | Tools for recent errors, mastered structures and open topics | A short learner snapshot is also in the prompt; the tools fetch more on demand. |
| Structured note-taking | A summarizer writes a card after each session; the next session reads it | Memory across sessions, not compaction of a running window. |
| Single-purpose model calls | Tutor, evaluator, summarizer, reflector | Each has its own prompt, model, reasoning effort and structured output. They are not autonomous sub-agents. |

## Workflow patterns

**Routing, by rules.** The support level picks the beginner loop or the conversation tutor; the mode picks the prompt; the CEFR level decides whether the length gate runs. No model call, so it costs nothing and is unit-testable.

**Generate, check, rewrite.** Adapted from evaluator-optimizer. In the original, a second model evaluates. Mine started that way and moved to code for length and tense limits ([ADR 2](../adr/0002-length-limits-in-code.md)). A rejected draft is rewritten once, with the reason. A short opener is simply dropped. An empty reply becomes a fallback sentence.

**Deferred evaluation.** Grading runs after the reply is sent, off the path the learner is waiting on.

**Human-in-the-loop reflection.** When a learner disputes a grade, a reflector reviews it and may add a versioned calibration note that the evaluator reads from then on (at most ten). It is *inspired by* [Reflexion](https://arxiv.org/abs/2303.11366), with a different trigger: a person's dispute instead of the agent's own trial feedback. I have not measured its effect on grading quality.

## Classic patterns

**Strategy and Adapter** (Gamma et al.). One interface for language model providers, with implementations for Gemini, OpenAI, Anthropic and OpenAI-compatible servers. Each role is configured with a provider and model. Only Gemini was used in the measurements; the others are covered by unit tests.

**Finite state machine.** The device moves through explicit states (provisioning, idle, listening, thinking, speaking, error), so it never records while it is talking.

**Local-first gatekeeping.** Wake word, noise gate and repetition scoring run without a model call.
