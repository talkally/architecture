# ADR-005: Config-Driven Function Tools via n8n Per Tenant

**Date:** November 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly supports 40+ AI functions across voice and text modes. Different user profiles (personal, business, tester) require different function sets. Hardcoding function configurations per profile in the app creates a maintenance burden and requires app updates for configuration changes.

---

## Decision

Implement a `FunctionConfigService` that fetches function definitions from an n8n webhook endpoint at runtime, per connection profile. This enables per-tenant function configuration without app updates.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Hardcoded function lists per profile | App update required for every config change |
| Remote config (Firebase) | Additional dependency, vendor lock-in |
| Local JSON config file | Cannot be changed without app update |

---

## Consequences

**Positive:**
- Function sets configurable per tenant without app release
- Enables A/B testing of function sets during closed testing
- Clean separation of function definition from function implementation

**Negative:**
- Network dependency at session initialisation
- n8n endpoint availability affects function loading
- Latency added to session startup

---

## Related Decisions
- ADR-003: n8n backend orchestration
- ADR-001: Dual AI provider architecture
