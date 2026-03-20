# TalkAlly — Architecture Decision Records

This directory contains Architecture Decision Records (ADRs) for the TalkAlly platform.

ADRs document significant architectural decisions made during development — what was decided, why, what alternatives were considered, and what the consequences are.

## Index

| ADR | Title | Status |
|---|---|---|
| [ADR-001](ADR-001-dual-ai-provider-architecture.md) | Dual AI Provider Architecture (OpenAI + Gemini) | Accepted |
| [ADR-002](ADR-002-flutter-single-codebase.md) | Flutter as Single Cross-Platform Codebase | Accepted |
| [ADR-003](ADR-003-n8n-backend-orchestration.md) | n8n on Hostinger VPS as Backend Orchestration | Accepted |
| [ADR-004](ADR-004-sqlcipher-local-storage.md) | SQLCipher for All Local Storage | Accepted |
| [ADR-005](ADR-005-config-driven-function-tools.md) | Config-Driven Function Tools via n8n Per Tenant | Accepted |
| [ADR-006](ADR-006-gemini-live-model-lock.md) | Gemini Live Model Locked to Specific Version | Accepted |
| [ADR-007](ADR-007-context-trim-at-turn-6.md) | Context Trim at Turn 6 | Accepted |
| [ADR-008](ADR-008-gemini-thinking-budget-zero.md) | thinkingBudget:0 for Gemini Text Mode with Functions | Accepted |
| [ADR-009](ADR-009-function-grouping-refactor-dropped.md) | Function Grouping Refactor Dropped After 1011 Root Cause Confirmed | Superseded |
| [ADR-010](ADR-010-pcm-audio-buffer-replay.md) | PCM Audio Buffer Replay on 1011 Crash Recovery | Accepted |
| [ADR-011](ADR-011-imap-app-password-over-gmail-api.md) | IMAP + App Password Over Gmail API | Accepted |
| [ADR-012](ADR-012-google-oauth-force-refresh-token.md) | Google OAuth with forceCodeForRefreshToken:true | Accepted |
| [ADR-013](ADR-013-ms365-dual-oauth.md) | Microsoft 365 Dual OAuth — Quick + Advanced | Accepted |
| [ADR-014](ADR-014-flutter-secure-storage-oauth.md) | FlutterSecureStorage for OAuth Tokens | Accepted |
| [ADR-015](ADR-015-api-key-pool-sqlite-migration.md) | API Key Pool Migration from Google Sheets to SQLite | Accepted |
| [ADR-016](ADR-016-trial-tracking-sqlite.md) | Trial Tracking in n8n SQLite | Accepted |
| [ADR-017](ADR-017-n8n-email-delivery.md) | n8n Webhook for Email Delivery | Accepted |
| [ADR-018](ADR-018-privacy-first-architecture.md) | Privacy-First, On-Device Architecture Principle | Accepted |
| [ADR-019](ADR-019-tester-end-user-build-flags.md) | Tester vs End-User Build Flag System | Accepted |
| [ADR-020](ADR-020-android-exact-alarm-permissions.md) | Android Notification Exact Alarm Permissions | Accepted |

## ADR Format

Each ADR follows this structure:
- **Context** — what situation prompted the decision
- **Decision** — what was decided
- **Alternatives Considered** — what else was evaluated and why it was rejected
- **Consequences** — positive and negative outcomes of the decision

## Adding New ADRs

Create a new file following the naming convention: `ADR-NNN-short-title.md`
Increment the number sequentially. Update this index.
