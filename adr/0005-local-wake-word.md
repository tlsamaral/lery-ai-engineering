# ADR 5: Detect the wake word on the device

**Status:** accepted, model still being improved

## Context

The device is always on in a room. It needs to know when it is being spoken to without sending the room to the cloud.

## Decision

A custom "Hey Lery" classifier on top of openWakeWord embeddings runs on the Raspberry Pi 3. Audio leaves the device only after activation. Two consecutive frames above the threshold are required.

## Alternatives

- **Stream audio and detect in the cloud.** Better accuracy, unacceptable for privacy and cost.
- **Physical button.** Reliable, but breaks the hands-free conversation.
- **A stock wake word.** No training needed, but the tutor would not answer to its own name.

## What happened

- **Version 1 failed.** The 718 positive samples held only 36 distinct recordings, because the speech synthesizer repeats itself. No noise, no real voice.
- **The name is heard as "Larry".** I added "Larry", "Leary" and "Lary" as positives, accepting that a real "Larry" wakes the device.
- **Real voice mattered most.** On 15 held-out recordings, the synthetic-only model detected 53% (threshold 0.9). Adding recordings of my voice gave 100% at 0.85 to 0.95.

## Consequences

- The 100% comes from one speaker and 15 samples, with 0 false activations in 3.3 minutes. It is not evidence for other voices.
- On openWakeWord's validation audio the same model gave about 6 false activations per hour at threshold 0.95 and 13 at 0.9. That is too high for a finished product.
- Next: more speakers, and long real-room recordings as negatives.
