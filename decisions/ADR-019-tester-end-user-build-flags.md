# ADR-019: Tester vs End-User Build Flag System

**Date:** November 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly requires different behaviour for closed testing (tester accounts, managed API keys, $10 limit) vs end-user production (personal API keys, $1 trial). A mechanism to distinguish these modes without separate app builds is needed for Google Play closed testing.

---

## Decision

Implement a build flag system in TrialService with two modes: TESTER (managed key pool, $10 limit, n8n backend) and END_USER (personal keys, $1 trial, in-app tracking). Mode determined at runtime via connection profile configuration, not compile-time flags.

---

## Consequences

**Positive:**
- Single app binary for both modes
- Mode switchable via profile configuration
- Clean separation of tester and production billing logic

**Negative:**
- Both code paths must be maintained and tested
- Risk of tester-mode behaviour leaking to production if profile misconfigured

---

## Related Decisions
- ADR-015: API key pool SQLite migration
- ADR-016: Trial tracking in n8n SQLite
