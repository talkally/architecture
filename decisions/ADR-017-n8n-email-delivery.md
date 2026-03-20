# ADR-017: n8n Webhook for Email Delivery

**Date:** November 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly supports sending emails via voice and text commands. Direct SMTP from a mobile app requires embedding credentials and managing deliverability. An abstraction layer via n8n provides flexibility to change email providers without app updates.

---

## Decision

Route all outbound email through an n8n webhook (talkally-dev-email). The app POSTs recipient, subject, and body to the webhook. n8n handles SMTP delivery via configured email provider. This abstracts email infrastructure from the app entirely.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Direct SMTP from app | Requires embedding SMTP credentials in app binary |
| SendGrid/Mailgun SDK in app | Vendor SDK dependency, API key exposure risk |
| Native Android email intent | Opens email app — breaks voice-driven send flow |

---

## Consequences

**Positive:**
- Email provider changeable without app update
- No SMTP credentials in app binary
- Consistent with n8n-as-backend pattern

**Negative:**
- n8n dependency for email delivery
- Potential content routing issue investigated (voice mode email sent shopping list content instead of notes — traced to n8n workflow, deferred)

---

## Related Decisions
- ADR-003: n8n backend orchestration
