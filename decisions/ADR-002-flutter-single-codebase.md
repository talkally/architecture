# ADR-002: Flutter as Single Cross-Platform Codebase

**Date:** October 2025  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

TalkAlly targets Android as primary platform with iOS as a future target. Native development would require maintaining separate Kotlin/Java (Android) and Swift (iOS) codebases. The app includes complex UI (chat bubbles, voice visualisation, settings screens) and deep device integrations (audio, notifications, contacts, SMS). A cross-platform framework was evaluated to reduce development overhead while maintaining production quality.

---

## Decision

Use Flutter (Dart) as the single codebase for all platforms. Android is the primary build target for Google Play closed testing. The same codebase will be used for future iOS submission without architectural changes.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Native Android (Kotlin) + Native iOS (Swift) | Two codebases, double maintenance, slower iteration |
| React Native | JavaScript bridge overhead unacceptable for real-time audio streaming |
| Kotlin Multiplatform | Immature UI layer at time of decision |

---

## Consequences

**Positive:**
- Single codebase for Android and iOS
- Rich widget library suitable for complex chat UI
- Strong Google ecosystem integration (Gemini, Flutter used by Tabcorp, BMW, Toyota at scale)
- Large developer community and active package ecosystem
- Hot reload accelerates UI iteration

**Negative:**
- Dart is a less common language — smaller talent pool for future hires
- Some platform-specific integrations require native code bridges
- App size larger than equivalent native app

---

## Related Decisions
- ADR-001: Dual AI provider architecture
- ADR-004: SQLCipher for local storage
