# ADR-014: FlutterSecureStorage for OAuth Tokens

**Date:** October 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly stores OAuth tokens for Google Workspace and Microsoft 365 integrations. These tokens grant access to user email, calendar, and contacts — high-sensitivity credentials requiring secure storage separate from general app data.

---

## Decision

Store all OAuth tokens (access tokens, refresh tokens, server auth codes) in `FlutterSecureStorage`, which uses the platform's native secure enclave — Android Keystore on Android, iOS Keychain on iOS. This is separate from the SQLCipher database used for user data.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| SQLCipher database | Appropriate for user data but not ideal for credentials — platform keychain preferred for tokens |
| SharedPreferences | No encryption — unacceptable for OAuth tokens |
| In-memory only | Tokens lost on app restart — requires re-authentication every session |

---

## Consequences

**Positive:**
- Tokens protected by platform hardware security where available
- Standard industry practice for mobile OAuth token storage
- Separate from user data — tokens can be cleared independently

**Negative:**
- FlutterSecureStorage has known issues on some Android devices with backup/restore
- Platform-specific behaviour differences between Android Keystore and iOS Keychain
- Token migration complexity if storage mechanism changes in future

---

## Related Decisions
- ADR-004: SQLCipher for local storage
- ADR-012: Google OAuth refresh token
- ADR-013: MS365 dual OAuth
