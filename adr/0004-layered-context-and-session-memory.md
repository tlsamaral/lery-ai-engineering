# ADR 4: Layered context and memory between sessions

**Status:** accepted

## Context

A tutor that forgets yesterday asks your name again. Replaying full transcripts grows cost and latency with every session and buries the few facts that matter.

## Decision

The system prompt is assembled in layers, most stable first: persona, tutor style, learner snapshot, session state (level rules and limits), then the lesson prompt, which overrides general behavior. When a session ends, a summarizer writes a short card: main error, progress, topic left open. The next session reads it. The tutor can also query the learner snapshot through tools. Diagnosis sessions use their own prompt.

## Alternatives

- **Send the whole history.** No information lost, cost and noise grow without limit.
- **Vector retrieval over transcripts.** Useful at much larger histories; one learner's facts fit in a small structured card.
- **No memory.** Cheapest, and the conversation never goes anywhere.

## Consequences

- Prompt size stays bounded.
- Production and the test lab share one assembly function.
- A bad summary carries into the next session.
- The memory probe passed in the four final runs. I have not run an ablation, so I cannot say how much memory improves the tutor.
