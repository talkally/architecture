# ADR-009: Function Grouping Refactor Dropped After 1011 Root Cause Confirmed

**Date:** March 2026  
**Status:** Superseded / Dropped  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly's Gemini Live voice mode experienced frequent 1011 WebSocket disconnection errors. The initial hypothesis was that exceeding ~47 function declarations caused these crashes, leading to a planned refactor to consolidate 47 functions into ~27 by grouping related functions (shopping 8→1, notes 4→1, reminders 4→1, etc.).

---

## Decision

The function grouping refactor was dropped after research confirmed the actual root cause of 1011 errors. The Gemini Live 1011 error (`extensible_stubs::OVERLOADED_TOO_MANY_RETRIES_PER_REQUEST`) is caused by Google's backend infrastructure overload specific to the preview model — not by function declaration count. The actual hard function declaration limit is 512 (not ~47 as initially assumed).

---

## Evidence

- Gemini Live WebSocket close code 1011 = server-side overload error
- Google's backend infrastructure intermittently rejects connections under load
- Function count of 47 is well within the 512 hard limit
- 1011 errors occur regardless of function count during peak load periods

---

## Alternatives Considered

| Option | Decision |
|---|---|
| Proceed with grouping refactor | Dropped — does not address root cause |
| Improve 1011 retry/recovery UX | Accepted — see ADR-010 |
| Wait for Gemini Live GA model | Noted — GA estimated Q2 2026 |

---

## Consequences

**Positive:**
- Avoids unnecessary architectural complexity
- Preserves granular function definitions — better AI tool selection accuracy
- Development time redirected to actual fix (retry mechanism)

**Negative:**
- 1011 errors remain until Google stabilises preview model infrastructure
- Recovery UX must compensate for infrastructure instability

---

## Related Decisions
- ADR-006: Gemini Live model lock
- ADR-010: PCM audio buffer replay on 1011 crash
