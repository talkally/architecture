# ADR-015: API Key Pool Migration from Google Sheets to SQLite

**Date:** January 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly's multi-user closed testing requires an API key pool to distribute OpenAI and Gemini API keys across concurrent sessions without exposing keys in the app. The initial implementation used Google Sheets as the key pool database, accessed via n8n workflows. Google Sheets API response times were consistently 3-4 seconds per operation, creating unacceptable session startup latency.

---

## Decision

Migrate the API key pool from Google Sheets to SQLite (stored on the n8n Hostinger VPS at `/home/node/.n8n/talkally-keypool.db`). n8n workflows updated to use the SQLite node for key acquire, release, and cleanup operations.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Google Sheets (status quo) | 3-4 second response time unacceptable for session startup |
| PostgreSQL | Overkill for key pool size, additional hosting cost |
| Redis | Additional infrastructure to manage |
| Firebase Realtime Database | Vendor lock-in, cost at scale |

---

## Consequences

**Positive:**
- Response time reduced from 3-4 seconds to ~10ms
- No additional infrastructure — SQLite runs on existing VPS
- Same n8n webhook endpoints — no app changes required
- Lightweight and appropriate for key pool data volume

**Negative:**
- SQLite on VPS has no replication — single point of failure
- Manual backup required for key pool data
- Not suitable if key pool grows beyond single-VPS capacity

---

## Related Decisions
- ADR-003: n8n backend orchestration
- ADR-019: Tester vs end-user build flag system
