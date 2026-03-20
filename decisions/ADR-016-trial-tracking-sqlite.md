# ADR-016: Trial Tracking in n8n SQLite

**Date:** January 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly's closed testing requires usage limits per device — tester accounts limited to $10 AUD, end-user accounts limited to $1 AUD. Initial trial tracking used Google Sheets, with the same latency issues as the key pool. Additionally, personal mode users should not require n8n infrastructure at all — trial tracking for personal mode should be fully in-app.

---

## Decision

Migrate trial tracking from Google Sheets to SQLite on the n8n VPS for tester/managed accounts. Personal mode users use in-app local tracking only — no n8n dependency.

---

## Consequences

**Positive:**
- Consistent ~10ms response time for trial checks
- Personal mode fully independent of backend infrastructure
- Separation of managed vs personal trial tracking

**Negative:**
- Two tracking implementations to maintain (in-app vs n8n)
- VPS SQLite has no replication

---

## Related Decisions
- ADR-015: API key pool SQLite migration
- ADR-019: Tester vs end-user build flag system
