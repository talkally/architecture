# ADR-003: n8n on Hostinger VPS as Backend Orchestration Layer

**Date:** November 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly requires a backend layer for API key pool management, trial tracking, email delivery, and config distribution. Options ranged from a custom REST API to a no-code/low-code workflow engine. Given a single-developer team with rapid iteration requirements, a workflow automation platform was evaluated.

---

## Decision

Use n8n (self-hosted on Hostinger VPS) as the backend orchestration layer. All backend functions are exposed as n8n webhooks consumed by the Flutter app. This provides a config-driven, per-tenant architecture without requiring a custom API codebase.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Custom Node.js/Express API | Higher development overhead, no visual workflow debugging |
| Firebase Functions | Vendor lock-in, cost unpredictability at scale |
| Managed n8n cloud | Higher cost, less control over data sovereignty |
| No backend (fully in-app) | Viable for personal mode but insufficient for multi-user key pool management |

---

## Consequences

**Positive:**
- Rapid workflow iteration without deployment cycles
- Visual debugging of webhook flows
- SQLite node available for lightweight database operations
- Config changes deployable without app updates

**Negative:**
- Single point of failure — VPS downtime affects all backend-dependent functions
- n8n is not designed for high-throughput production load
- Hostinger VPS introduces latency vs cloud-native alternatives (3-4s for Google Sheets operations — later resolved by migrating to SQLite)

---

## Related Decisions
- ADR-015: API key pool SQLite migration
- ADR-017: n8n webhook for email delivery
