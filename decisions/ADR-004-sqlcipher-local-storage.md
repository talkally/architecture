# ADR-004: SQLCipher for Local Storage

**Date:** November 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly stores things users genuinely care about — their conversations, notes, reminders, shopping lists. For an AI assistant this is sensitive data by definition. The standard Flutter SQLite package (`sqflite`) writes everything to disk in plaintext. On a rooted Android device or via ADB backup, anyone can read it.

This was not acceptable for product I want to position as privacy-first, particularly for Australian market where privacy expectations are reasonable and I have clients in defence-adjacent space (DISP work through INTERprise) who take data handling seriously.

---

## What Was Decided

Use `sqflite_sqlcipher` — SQLCipher-encrypted SQLite — for all structured local data. OAuth tokens go into `FlutterSecureStorage` separately (platform keychain). Nothing sensitive in SharedPreferences.

The encryption is transparent to the rest of the codebase — same SQL queries, same Flutter patterns, just with encrypted database file on disk.

---

## Alternatives I Looked At

**Standard sqflite** — rejected immediately. No encryption, plaintext on disk.

**Hive** — considered it. Fast, Flutter-native, good for key-value data. But adding encryption requires separate package, and the query model doesn't suit relational data like notes + reminders + shopping lists that have relationships between them.

**Realm** — too heavy for this use case. MongoDB's licensing model for Realm also gave me pause.

**Cloud-only storage** — completely contrary to privacy-first principle. Users' personal notes should not leave their device by default. This was a non-starter.

---

## How It Turned Out

Mostly straightforward. The SQLCipher key initialisation adds a small amount of complexity at app startup, and debugging requires SQLCipher-aware database viewer (can't just open the .db file with standard tools). Neither has been a real problem in practice.

Performance overhead is negligible — we're not doing high-frequency writes, just user-initiated actions like saving notes or adding reminders.

One thing worth noting: `sqflite_sqlcipher` package maintenance is community-driven and occasionally lags behind Flutter updates. Worth watching this dependency at each Flutter upgrade.

---

## Related Decisions
- ADR-014: FlutterSecureStorage for OAuth tokens
- ADR-018: Privacy-first architecture principle
