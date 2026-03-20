# ADR-004: SQLCipher for All Local Storage

**Date:** October 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly stores sensitive user data locally — chat history, notes, reminders, shopping lists, and OAuth tokens. Australian privacy expectations and the app's positioning as a privacy-first assistant require that all local data be encrypted at rest. Standard SQLite provides no encryption. OAuth tokens require additional protection beyond standard SQLite.

---

## Decision

Use `sqflite_sqlcipher` (SQLCipher-encrypted SQLite) for all structured local data storage. OAuth tokens are stored separately in `FlutterSecureStorage` (platform keychain). No sensitive data is stored in plaintext SharedPreferences.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Standard SQLite (sqflite) | No encryption at rest — unacceptable for chat history and notes |
| Hive | No built-in encryption without additional package complexity |
| Realm | Heavier dependency, commercial licensing considerations |
| Cloud-only storage | Violates privacy-first design principle — data sovereignty requirement |

---

## Consequences

**Positive:**
- All user data encrypted at rest by default
- Consistent with privacy-first design principle
- Meets Australian Privacy Act expectations for sensitive personal data
- Single encrypted database for all structured local data

**Negative:**
- Slight performance overhead vs plaintext SQLite (negligible for mobile use cases)
- SQLCipher key management adds initialisation complexity
- Harder to inspect database during development (requires SQLCipher-aware tooling)

---

## Related Decisions
- ADR-018: Privacy-first architecture principle
- ADR-014: FlutterSecureStorage for OAuth tokens
