# ADR-008: thinkingBudget:0 for Gemini Text Mode with Functions

**Date:** December 2025
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

During Gemini text mode function calling implementation, a critical silent failure was discovered: Gemini 2.5 Flash's thinking mode consumes the entire token budget when function declarations are present, returning `finishReason=STOP` with zero output tokens and empty content parts. This produces no visible error — the app simply hangs or returns blank responses.

---

## Decision

Set `thinkingConfig: {thinkingBudget: 0}` in `generationConfig` whenever function declarations are loaded in Gemini text mode. This explicitly suppresses thinking mode, preventing silent token exhaustion.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Rely on default thinking config | Causes silent blank response — unacceptable in production |
| Increase max token limit | Does not resolve root cause — thinking still consumes budget |
| Remove function declarations | Defeats the purpose of text mode function calling |
| Use different model | Other models lack function calling quality required |

---

## Consequences

**Positive:**
- Eliminates silent blank response failures in text mode with functions
- Reduces token consumption — no thinking tokens billed
- Deterministic response generation

**Negative:**
- Disables extended thinking capability — complex reasoning tasks use standard generation only
- Must be revisited if Google resolves the function declaration + thinking interaction

---

## Notes

A defensive retry-without-functions fallback was also added for any future null content responses as a belt-and-suspenders measure.

---

## Related Decisions
- ADR-007: Context trim at turn 6
