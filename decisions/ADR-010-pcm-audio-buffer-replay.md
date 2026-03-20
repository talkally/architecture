# ADR-010: PCM Audio Buffer Replay on 1011 Crash Recovery

**Date:** March 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

When a Gemini Live 1011 crash occurs mid-conversation, the app reconnects and attempts to replay the user's last request. Prior implementation relied on `inputTranscription` (text transcript) for replay. However, the transcript arrives too late in ~70% of crashes — causing the AI to respond with "I'm back, how can I help?" instead of answering the original question. This made the recovery experience feel broken during testing.

---

## Decision

Implement a PCM audio buffer in `gemini_live_service.dart` that captures raw audio chunks during the user's active speech turn. On 1011 crash recovery, replay the buffered PCM audio rather than relying on the text transcript. The buffer is preserved across the disconnect/reconnect cycle via a `preserveSpeechBuffer` parameter on `connect()`.

---

## Alternatives Considered

| Option | Reason Rejected |
|---|---|
| Text transcript replay only | Transcript arrives too late in ~70% of cases — unreliable |
| Show error and ask user to repeat | Poor UX — makes crash visible and burdens user |
| Increase retry delays to wait for transcript | Adds latency to happy path — unacceptable |
| Ignore 1011 and force full reconnect | User loses context entirely |

---

## Implementation Details

- `hasSpeechAudioBuffer` getter exposes buffer availability to `chat_screen.dart`
- `replaySpeechAudio()` method sends buffered PCM on reconnect
- `_preserveSpeechBufferOnNextConnect` flag prevents buffer clear on reconnect
- Buffer snapshot taken before `_disconnectVoice()` to survive the disconnect cycle
- Retry counter reset moved — counters no longer reset on transcript arrival (which includes replayed transcripts), only on successful AI response

---

## Consequences

**Positive:**
- Recovery from 1011 is invisible to user in happy path (attempts 1-2 silent)
- Correct question answered after crash recovery in majority of cases
- User warning bubble only shown when all retry attempts exhausted

**Negative:**
- PCM buffer consumes memory during active speech turns
- Buffer management adds state complexity to `gemini_live_service.dart`
- Does not eliminate 1011 crashes — only improves recovery

---

## Related Decisions
- ADR-009: Function grouping refactor dropped
- ADR-006: Gemini Live model lock
