# ADR-006: Gemini Live Model Locked to gemini-2.5-flash-native-audio-preview

**Date:** February 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

Gemini Live API offers multiple model variants. During development, model selection was not explicitly locked, causing silent failures when Google updated default model routing. Testing confirmed that only one model produces consistent real-time native audio behaviour suitable for TalkAlly's voice mode.

---

## Decision

Explicitly lock the Gemini Live service to model `gemini-2.5-flash-native-audio-preview-12-2025`. Any other model string, including generic aliases, must not be used in production voice mode.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Use generic `gemini-live` alias | Routes to wrong model — confirmed by testing, produces incompatible audio format |
| Use `gemini-2.0-flash-live` | Different audio pipeline, not native audio — unacceptable voice quality |
| Dynamic model selection | Risk of silent degradation on Google-side routing changes |

---

## Consequences

**Positive:**
- Deterministic model behaviour in production
- Eliminates silent audio pipeline failures from model routing changes
- Explicit model string surfaced in config for easy future update

**Negative:**
- Must be manually updated when Google releases a stable GA model
- Preview model may be deprecated without notice
- Locked to AI Studio availability — Vertex AI GA estimated Q2 2026

---

## Notes

The Vertex AI GA of the stable model occurred December 2025. AI Studio GA estimated Q2 2026. Model string must be reviewed at that point.

---

## Related Decisions
- ADR-001: Dual AI provider architecture
- ADR-009: Function grouping refactor dropped after 1011 root cause confirmed
