# ADR-011: IMAP + App Password Over Gmail API

**Date:** February 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly requires email reading capability for Gmail accounts. The standard approach is the Gmail API using OAuth with `gmail.readonly` scope. However, Google requires a CASA (Cloud Application Security Assessment) Tier 2 audit for apps requesting sensitive Gmail scopes. CASA Tier 2 audit costs approximately AUD $4,500–$8,000 and takes 4–6 weeks — prohibitive for a bootstrapped product at closed testing stage.

---

## Decision

Implement Gmail reading via IMAP protocol using an App Password instead of the Gmail API. Users generate a Gmail App Password (requires 2FA enabled on Google account) and store it in TalkAlly. IMAP connects to `imap.gmail.com:993` using the App Password credential.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Gmail API with `gmail.readonly` scope | Requires CASA Tier 2 audit (~$4,500-8,000 AUD, 4-6 weeks) |
| Gmail API with restricted scope | Insufficient for email reading functionality |
| Defer email reading entirely | Removes a core feature from the product |

---

## Consequences

**Positive:**
- Avoids CASA audit cost and delay — feature ships immediately
- IMAP is a stable, well-documented protocol
- App Password stored encrypted in SQLCipher alongside other credentials
- No additional OAuth scope approval required

**Negative:**
- Requires user to generate and manage an App Password — higher setup friction
- App Password grants broad IMAP access — not scope-limited like OAuth
- Requires user to have Google 2FA enabled
- Must be revisited when pursuing Gmail API CASA audit for production launch

---

## Future State

CASA Tier 2 audit should be pursued before public launch to migrate to OAuth `gmail.readonly` scope and remove App Password requirement.

---

## Related Decisions
- ADR-004: SQLCipher for local storage
- ADR-012: Google OAuth with forceCodeForRefreshToken
