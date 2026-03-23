# ADR-001: Dual AI Provider Architecture (OpenAI + Gemini)

**Date:** November 2025
**Status:** Accepted
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

When I started thinking about TalkAlly, OpenAI Realtime API was the only serious option for real-time voice AI on mobile. Gemini Live was announced shortly after and looked promising - better pricing, native audio pipeline, Google ecosystem integration. Rather than pick one and commit, I decided to support both from the beginning.

The risk of single-provider dependency was obvious. If OpenAI changes pricing or rate limits, the whole product is affected. With two providers wired in parallel, users can switch via connection profile without losing anything.

This was not a trivial decision. It means every new feature has to be built twice - once for `realtime_service.dart` and once for `gemini_live_service.dart`. At this stage of the product, resilience matters more than development speed.

---

## What Was Decided

Support both OpenAI Realtime API (WebSocket) and Gemini Live API (WebSocket) as fully interchangeable runtime providers. Provider selection happens at the connection profile level - users choose, not the app. Both services implement the same functional surface area, though the underlying API behaviour differs in places (function calling format, audio handling, error recovery).

---

## What I Considered and Rejected

Going OpenAI-only was the simpler path. Their API is more stable and documentation is better. But the pricing model at scale concerned me, and having no fallback felt wrong for a voice assistant that people would rely on daily.

Gemini-only was not viable at the time - the preview model was new and unproven in production. Not enough confidence to bet the product on it exclusively.

I looked at building an abstraction layer to hide the differences between providers behind a unified interface. Decided against it - the APIs behave differently enough that a clean abstraction would require significant engineering time, and I wasn't sure yet which behaviours I actually needed to abstract. Deferred this to a future decision.

---

## Consequences

The dual maintenance surface is the main ongoing cost. Every function handler, every error recovery path, every new integration has to work on both providers. I expect this to slow things down as the function library grows.

The upside is user choice. Some users will prefer OpenAI voice quality, others may prefer Gemini's pricing or Google ecosystem integration. Giving them the choice at profile level felt like the right call.
