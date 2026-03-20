# ADR-018: Privacy-First, On-Device Architecture Principle

**Date:** October 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly handles sensitive personal data — conversations, notes, reminders, contacts, calendar events. The Australian Privacy Act and user expectations for an AI assistant require a clear data handling position. Two architectural philosophies were available: cloud-first (all data synced to backend) or privacy-first (data stays on device by default).

---

## Decision

Adopt privacy-first as a core architectural principle. All user data stored locally in SQLCipher. No user content transmitted to TalkAlly servers. AI processing occurs via user's own API provider accounts. Backend infrastructure (n8n) handles only operational data (key pool, trial tracking) — never user content.

---

## Consequences

**Positive:**
- Strong privacy position for Australian market
- Differentiates from cloud-first AI assistants
- Reduces TalkAlly's data liability and compliance burden
- Aligns with DISP/defence-adjacent client requirements

**Negative:**
- No cross-device sync without additional architecture
- Backup/restore requires explicit export feature

---

## Related Decisions
- ADR-004: SQLCipher for local storage
