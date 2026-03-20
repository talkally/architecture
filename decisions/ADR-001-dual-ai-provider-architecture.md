# ADR-001: Dual AI Provider Architecture (OpenAI + Gemini)

**Date:** November 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly requires a real-time voice and text AI backend. Two leading providers offer real-time multimodal APIs suitable for production mobile use: OpenAI Realtime API (WebSocket) and Google Gemini Live API (WebSocket). A single-provider dependency introduces availability risk, cost lock-in, and limits capability differentiation across user segments.

---

## Decision

Implement a dual AI provider architecture supporting both OpenAI Realtime API and Gemini Live API as fully interchangeable runtime providers. Users select their provider via Connection Profiles. The app maintains separate service implementations (`realtime_service.dart`, `gemini_live_service.dart`) behind a unified interface consumed by `chat_screen.dart`.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| OpenAI only | Single vendor dependency, higher cost per token, no fallback |
| Gemini only | Preview model instability (1011 crashes), no fallback |
| Abstraction layer / adapter pattern | Premature abstraction given divergent API behaviours — deferred |

---

## Consequences

**Positive:**
- Provider failover available at user level via profile switching
- Cost optimisation — users can select cheaper provider per use case
- Competitive feature parity maintained across both ecosystems
- Enables A/B capability comparison during closed testing

**Negative:**
- Dual maintenance surface — every new feature requires implementation in both services
- Divergent API behaviours (function calling format, audio handling, error codes) increase complexity
- Testing matrix doubles for voice mode features

---

## Related Decisions
- ADR-002: Flutter single codebase
- ADR-006: Gemini Live model version lock
- ADR-008: Gemini thinking budget suppression
