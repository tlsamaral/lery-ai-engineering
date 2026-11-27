# ADR 1: No provider keys on the device

**Status:** accepted

## Context

The first version called the language model straight from the Raspberry Pi, with the provider key in a `.env` file on the SD card. The device sits in someone's home. Whoever holds the card holds the key, and rotating it means touching every device. Prompts and level rules also lived on the device, so every pedagogical change meant updating hardware.

## Decision

A server-side orchestration service owns every provider call: transcription, language model and commercial voice. The device holds a single credential of its own, revocable per device and valid only against our services.

## Alternatives

- **Keep the SDK on the device.** Simplest and one hop shorter, but the key is exposed and behavior is frozen into each install.
- **Short-lived provider tokens on the device.** Limits exposure, but leaves prompts and rules on the hardware and ties the design to what each provider supports.

## Consequences

- Prompts, rules and models change in one place.
- The same code runs behind the device and the test lab.
- One more network hop and one more service to operate; it is now a point of failure.
- The device key still sits on the SD card. It is a smaller risk, not zero.
- Delivering that key to a new device during pairing is not built yet; it is provisioned by hand. This blocks production use.
