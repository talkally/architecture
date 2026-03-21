# ADR-001: Dual AI Provider Architecture (OpenAI + Gemini)

**Date:** November 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

When I started building TalkAlly, OpenAI Realtime API was the only serious option for real-time voice AI on mobile. Gemini Live was announced shortly after and looked promising — better pricing, native audio pipeline, Google ecosystem integration. Rather than pick one and commit, I decided to support both from the beginning.

The risk of single-provider dependency was obvious — if OpenAI changes pricing or rate limits, or if Gemini has stability issues (which it did — see ADR-009), the whole product is affected. With two providers wired in parallel, users can switch via connection profile without losing anything.

This was not a trivial decision. It means every new feature has to be built twice — once for `realtime_service.dart` and once for `gemini_live_service.dart`. But at this stage of the product, resilience matters more than development speed.

---

## What Was Decided

Support both OpenAI Realtime API (WebSocket) and Gemini Live API (WebSocket) as fully interchangeable runtime providers. Provider selection happens at the connection profile level — users choose, not the app. Both services implement the same functional surface area, though the underlying API behaviour differs significantly in places (function calling format, audio handling, error recovery).

---

## What I Considered and Rejected

Going OpenAI-only was the simpler path. Their API is more stable and documentation is better. But the pricing model at scale concerned me, and having no fallback felt wrong for a voice assistant that people would rely on daily.

Gemini-only was not viable at the time — the preview model had infrastructure instability (1011 errors, confirmed as server-side overload — not our problem). Still not fully resolved as of March 2026.

I looked at building an abstraction layer to hide the differences between providers behind a unified interface. Decided against it — the APIs behave differently enough that a clean abstraction would require significant engineering time, and I wasn't sure yet which behaviours I actually needed to abstract. Deferred this to a future decision.

---

## Consequences

The dual maintenance surface is the main ongoing cost. Every function handler, every error recovery path, every new integration has to work on both providers. This slowed down the text mode implementation noticeably — 23 function handlers had to be ported to both `gemini_chat_service.dart` and the OpenAI text service.

The upside: during closed testing, testers can compare providers directly. Some users prefer OpenAI voice quality, others prefer Gemini's response speed. Giving them the choice turned out to be a feature, not just an architecture decision.

---

## Related Decisions
- ADR-006: Gemini Live model version lock
- ADR-009: Function grouping refactor dropped after 1011 root cause confirmed
