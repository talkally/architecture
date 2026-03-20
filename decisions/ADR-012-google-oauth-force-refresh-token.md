# ADR-012: Google OAuth with forceCodeForRefreshToken:true

**Date:** March 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly uses Google Sign-In for Workspace integration (Calendar, Gmail, Contacts, Tasks). After initial OAuth authorisation, subsequent sign-ins did not return a new refresh token — only an access token. This caused silent authentication failures after access token expiry, requiring users to fully re-authenticate rather than silently refreshing.

---

## Decision

Add `forceCodeForRefreshToken: true` to the `GoogleSignIn` constructor configuration, and reset the GoogleSignIn singleton on each sign-in call. This forces Google to return a fresh server auth code on every sign-in, from which a new refresh token can be obtained via token exchange.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Default GoogleSignIn behaviour | Does not return refresh token on subsequent sign-ins |
| Manual token refresh without forceCodeForRefreshToken | Refresh token missing — cannot refresh |
| Re-authenticate user every session | Unacceptable UX — breaks hands-free use case |

---

## Consequences

**Positive:**
- Refresh token reliably obtained on every sign-in
- Silent background token refresh works correctly
- Re-authentication bottom sheet only shown when genuinely needed

**Negative:**
- Slight increase in sign-in latency (server auth code exchange adds one round trip)
- Users may see Google consent screen more frequently than expected

---

## Related Decisions
- ADR-011: IMAP + App Password over Gmail API
- ADR-014: FlutterSecureStorage for OAuth tokens
