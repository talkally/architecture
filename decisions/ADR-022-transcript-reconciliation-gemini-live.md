# ADR-022: Transcript Reconciliation for Gemini Live Voice Mode

**Date:** March 2026
**Status:** Accepted
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

Gemini Live returns two transcripts per turn - `inputTranscription` for what the user said and `outputTranscription` for what the AI responded. In theory this gives you a complete chat history. In practice, two things kept going wrong.

The first problem: when a 1011 crash happens and the session reconnects, `inputTranscription` arrives too late around 70% of the time. The AI comes back online and responds with something like "I'm back, how can I help?" - which sounds fine to the user but leaves a broken entry in history with no record of what was actually asked. Multiply this across a session and the history becomes unreliable.

The second problem is less obvious. Function call turns - creating a calendar event, sending an email, adding a shopping item - don't produce a conversational transcript at all. The AI acted, the user got a response, but history shows nothing. I only noticed this during a demo when I scrolled back through a session and couldn't tell what had happened in several turns.

Both problems matter more as the app moves toward public testing. A voice assistant that can't show what it did is hard to trust.

---

## What I Decided

Two-path correction, both changes contained in `chat_screen.dart`:

For function call turns, I'm using output-derived correction - the function result itself becomes the history entry. If `create_event_google` returns a confirmed event, that gets written into history as a readable summary of what was done. No transcript needed.

For conversational turns, I'm adding a Gemini Flash post-turn call to reconstruct `inputTranscription` when it's missing or arrived too late. Flash is cheap enough that the latency cost is acceptable. This runs after the primary turn completes so it doesn't block the voice pipeline.

The scope is intentionally narrow - `chat_screen.dart` only. Nothing changes in `gemini_live_service.dart` or the function handler.

---

## What I Considered

**Just accepting the gaps.** Tempting because it's zero work, but the history quality was genuinely bad enough to affect testing feedback. Ruled out.

**PCM audio buffer replay on crash.** Buffer raw audio during the user's speech turn and replay it on reconnect instead of relying on `inputTranscription`. This would fix the 1011 reconnect case but does nothing for function call turns, and it touches the audio pipeline in `gemini_live_service.dart` which I want to keep stable right now. Might revisit this separately.

---

## Outcome

History now reflects what actually happened across voice conversation and function activity. The 1011 reconnect entries that previously showed "I'm back" will now show the original question and answer. There's a small per-turn cost for the Flash correction call - I haven't measured it precisely yet but it hasn't been noticeable in testing.
