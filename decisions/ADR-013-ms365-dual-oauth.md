# ADR-013: Microsoft 365 Dual OAuth — Quick (PKCE) + Advanced (User Azure App)

**Date:** January 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly supports Microsoft 365 integration (Outlook, Calendar, Contacts, Teams). Enterprise M365 tenants have varying security policies — some allow third-party OAuth apps, others enforce strict conditional access requiring apps registered in the tenant's own Azure AD. A single OAuth flow cannot satisfy both consumer and enterprise M365 users.

---

## Decision

Implement two M365 OAuth modes available to users via Connection Profile settings:

- **Quick Mode:** PKCE flow using TalkAlly's registered Azure AD app. Suitable for personal M365 accounts and permissive enterprise tenants. Uses `app_links` deep linking for callback (`talkally://oauth/microsoft/callback`).
- **Advanced Mode:** User registers their own Azure AD app and provides Client ID. OAuth flow routes through n8n webhook for token exchange. Suitable for strict enterprise tenants with conditional access policies.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Quick Mode only | Blocked by enterprise conditional access policies |
| Advanced Mode only | Too complex for personal/SMB users |
| Single enterprise app registration | Cannot satisfy all tenant security policies |

---

## Consequences

**Positive:**
- Covers both consumer and enterprise M365 use cases
- Advanced mode future-proofs for enterprise sales
- Users self-select complexity level appropriate to their environment

**Negative:**
- Two OAuth flows to maintain
- Advanced mode requires user to understand Azure AD app registration
- n8n dependency for Advanced mode token exchange

---

## Related Decisions
- ADR-003: n8n backend orchestration
- ADR-014: FlutterSecureStorage for OAuth tokens
