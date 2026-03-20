# ADR-020: Android Notification Exact Alarm Permissions

**Date:** February 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly's reminder feature requires notifications to fire at precise scheduled times. Android 12+ introduced SCHEDULE_EXACT_ALARM as a restricted permission requiring explicit user grant. Standard AlarmManager without exact alarm permission produces unreliable notification delivery.

---

## Decision

Request SCHEDULE_EXACT_ALARM and USE_EXACT_ALARM permissions in AndroidManifest.xml and prompt users to grant exact alarm permission at first reminder creation. Show orange warning in Reminders UI when permission is denied, with a Settings action button.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Standard inexact alarms | Unreliable delivery — reminders fire late or not at all |
| WorkManager | Not designed for precise time-critical notifications |
| Firebase Cloud Messaging | Requires backend push infrastructure |

---

## Consequences

**Positive:**
- Reminders fire at exact scheduled time
- User clearly informed when permission missing
- One-tap path to Settings for remediation

**Negative:**
- Additional permission friction for users
- OEM battery optimisation may still delay on some devices

---

## Related Decisions
- ADR-018: Privacy-first architecture principle
