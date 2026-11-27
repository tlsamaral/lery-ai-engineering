# ADR 3: Prove physical presence with light colors

**Status:** accepted

## Context

The device has no screen or keyboard, and needs a Wi-Fi password sent from a phone over Bluetooth Low Energy. Before accepting it, the device should know the person holding the phone can see the device.

## Decision

The LED ring shows one of five colors and the app asks which one the learner sees. Two rounds, two different colors. The correct answer is never sent over the radio. Three failures lock pairing for 60 seconds, and the count survives reconnects. For people who cannot rely on color, a spoken four-digit code is available.

## Alternatives

- **Spoken code only.** Works, but is slower and fails in a noisy room. Kept as the fallback.
- **Temporary Wi-Fi hotspot with a captive portal.** Needs no app, but makes the user switch networks. Kept as the last resort.
- **Config file on the SD card.** Not acceptable for a consumer device.

## Consequences

- No typing and no printed secret.
- The link is encrypted but uses "Just Works" pairing, so it does **not** protect against an active man-in-the-middle. The color challenge proves presence; it is not authentication of the channel.
- Production would need a cryptographic device identity in the QR code and application-level encryption.
- Covered by automated tests. Not yet validated across a range of phones and routers.
