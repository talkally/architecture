# ADR-010: PCM Audio Buffer Replay on 1011 Crash Recovery

**Date:** March 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

The 1011 crash recovery problem was probably the most frustrating UX issue in the whole project. When Gemini Live drops with error 1011, the app reconnects — but the user's question is lost. Previous implementation tried to replay the question using `inputTranscription` (the text transcript of what the user said). This worked maybe 30% of the time.

The problem: transcript arrives asynchronously and in about 70% of crashes it simply hasn't arrived yet when the reconnect happens. So the AI reconnects, has no idea what was asked, and says something like "I'm back, how can I help?" — which looks completely broken to the user.

During closed testing this made the whole app feel unstable even when everything else was working correctly. It had to be fixed.

---

## What Was Decided

Buffer the raw PCM audio during active user speech turns. On 1011 crash and reconnect, replay the buffered audio instead of waiting for transcript. Audio is always available immediately — it's what we were receiving in real time. No async dependency.

Key implementation detail: the buffer had to survive the disconnect/reconnect cycle. First version cleared the buffer inside `connect()` before the recovery code could read it. Fixed by adding `preserveSpeechBuffer` parameter to `connect()` and snapshotting buffer availability before calling `_disconnectVoice()`.

Second bug found during testing: retry counters were resetting on every user transcript arrival — including replayed transcripts. So each crash looked like attempt #1 to the retry logic, and warning bubble appeared too early. Fixed by moving counter reset to successful AI response only, not transcript arrival.

---

## What I Considered

**Text transcript replay only** — the original approach. Failed in ~70% of cases as described. Not acceptable.

**Show error, ask user to repeat** — technically simplest. But breaks the hands-free use case entirely. If user is driving and the app crashes silently and recovers, they should not have to repeat themselves.

**Increase retry delays to wait for transcript** — tried this. Adds 2-3 seconds to every recovery path including cases where transcript arrives quickly. Makes the happy path slower.

---

## Current State

Recovery is now invisible in most cases — attempts 1 and 2 are silent, no user-visible bubble. Warning only shows when all attempts exhausted. This is the right behaviour. Users in testing stopped noticing crashes at all, which is the goal.

Does not eliminate 1011 crashes — that's Google's infrastructure problem with the preview model. Just makes recovery transparent.

---

## Related Decisions
- ADR-009: Function grouping refactor dropped — 1011 is infrastructure, not function count
- ADR-006: Gemini Live model lock
