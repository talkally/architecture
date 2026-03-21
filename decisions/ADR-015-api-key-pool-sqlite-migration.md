# ADR-015: API Key Pool — Migrated from Google Sheets to SQLite

**Date:** January 2026  
**Status:** Accepted  
**Author:** Roman Gudkov, INTERprise Technologies

---

## Context

For closed testing, TalkAlly needs to distribute API keys across multiple test users without giving each user their own OpenAI or Gemini key. The key pool manages this — acquire a key at session start, release it at session end, rotate across available keys.

First implementation used Google Sheets as the database, accessed via n8n Google Sheets node. It worked, but every key acquire and release operation took 3-4 seconds. For a voice assistant where session startup latency matters, this was not acceptable. Users noticed.

---

## What Was Decided

Migrate key pool to SQLite file on the n8n VPS (`/home/node/.n8n/talkally-keypool.db`). Updated n8n workflows to use SQLite node instead of Google Sheets node. Same webhook endpoints — no app changes required.

Result: 3-4 seconds → ~10ms. Problem solved.

---

## Why Not Something More Sophisticated

**PostgreSQL** — considered it. Overkill. The key pool has maybe 20-30 rows at most. Setting up and managing a separate database server for this is not justified.

**Redis** — same objection. Also adds infrastructure I'd need to maintain separately.

**Firebase Realtime Database** — vendor lock-in, pricing uncertainty at scale, overkill for the use case.

SQLite on the same VPS where n8n already runs is the right size solution for the problem. If TalkAlly scales to thousands of concurrent users, this decision needs revisiting. At current testing scale it's fine.

---

## One Thing Worth Noting

SQLite on VPS has no replication. If the VPS goes down, key pool is unavailable and new sessions can't start. Acceptable risk for closed testing phase. For production, either replicated SQLite or a proper managed database would be needed.

Also: the trial tracking system (Google Sheets → SQLite) was migrated at the same time using same pattern. See ADR-016.

---

## Related Decisions
- ADR-003: n8n backend orchestration
- ADR-016: Trial tracking SQLite migration
