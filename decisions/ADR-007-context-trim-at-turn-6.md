# ADR-007: Context Trim at Turn 6

**Date:** December 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

Gemini text mode maintains conversation history across turns. Unbounded history growth causes token cost escalation and risks hitting context window limits, producing degraded responses. A trim strategy is required to balance conversational continuity against cost and reliability.

---

## Decision

Trim conversation history to the last 6 turns (3 user + 3 assistant exchanges) before each API call in Gemini text mode. System prompt is always prepended fresh — it is not subject to trimming.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| No trimming | Unbounded token cost, context window overflow risk |
| Token-count-based trim | Complex to implement accurately across providers |
| Summarisation of older turns | High latency, additional API cost, complexity |
| Turn 3 trim | Too aggressive — loses useful recent context |
| Turn 10 trim | Too permissive for cost targets |

---

## Consequences

**Positive:**
- Predictable token cost per session
- Prevents context window overflow
- Simple, deterministic implementation

**Negative:**
- Conversational context lost after 6 turns — user may need to repeat context
- Not ideal for long analytical tasks requiring full history

---

## Related Decisions
- ADR-008: Gemini thinking budget suppression
