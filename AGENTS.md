# Agent instructions

This repository answers SCP:SL plugin-development questions. It does not build anything.

## Reading order

1. [kb/index.md](kb/index.md). It routes to a topic file in one hop. Do not grep blindly first.
2. The topic file. Each claim names its source path.
3. The cited source, if the claim is load-bearing for what you are about to write.

If the index has no entry for your topic, this knowledge base does not cover it. Fall back to
the source tree directly, in this order:

1. Official LabAPI source — plugin APIs, wrappers, events, loader, configs, commands.
2. Decompiled `Assembly-CSharp` — native dedicated-server gameplay code, for behavior LabAPI
   does not wrap or when designing a Harmony patch against real game flow.
3. Decompiled `Assembly-CSharp-firstpass` — MEC, Discord, support code.
4. Client sources — only for client-side questions, and only when asked.

Prefer a LabAPI wrapper or event over a decompiled game class whenever it covers the case.

## Trusting an entry

Every entry carries three tags. Read them before acting on the claim.

- **Source** — the path and symbol the claim came from.
- **Verified** — the game build, LabAPI version, and date.
- **Confidence** — `source-read` (traced through code) or `in-game-verified` (observed running).

If **Verified** predates the current server build, treat the claim as a lead, not a fact:
re-check the cited source before relying on it. Game updates silently invalidate entries;
nothing here re-validates itself.

## Adding an entry

Read [meta/contributing.md](meta/contributing.md) first. In short: a claim without a source
path and a verified-against build does not go in. Symptom-first phrasing for gotchas, because
agents arrive at them by symptom. Replace stale facts in place — do not append corrections
below the old text, and do not record the history of a bug here.
