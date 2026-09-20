# Contributing

## The evidence rule

An entry carries three tags or it does not go in:

```markdown
**Source:** LabApi/Features/Wrappers/Players/Player.cs, Player.TryGet
**Verified:** LabAPI 1.1.7 / SCP:SL 14.2.7 / 2026-09-20
**Confidence:** source-read
```

- **Source** — path and symbol. "The docs say" is not a source. If you cannot name where you
  read it, you do not know it.
- **Verified** — the build you checked against, and the date. A claim with no build is
  unfalsifiable later.
- **Confidence** — `source-read` (traced through code) or `in-game-verified` (observed on a
  running server). Do not label something `in-game-verified` that you only reasoned about.

## What belongs here

Write the entry only if it is one of:

- A **gotcha** — the API's behavior contradicts its obvious reading.
- A **lookup route** — where the authoritative answer for a topic lives.
- A **verified constant or formula** — measured, with the measurement method stated.
- A **minimal pattern** — the smallest correct thing, not a tutorial.

## What does not belong here

- Restatements of LabAPI XML docs or paraphrased source. An agent can read the source.
- Vendored LabAPI or decompiled game source. Cite paths; the tree is reconstructible.
- Product documentation for a specific plugin. That lives with the plugin.
- Deployment and operations detail, server addresses, machine-local paths.
- Bug history. "This used to crash before 14.2" helps nobody writing code today. Correct the
  entry in place; the commit log keeps the history.

## Writing a gotcha

Lead with the **symptom**, because that is how an agent arrives at it: the behavior is
already happening and they are trying to name it. Then the cause, then the fix.

Add a row to [../kb/gotchas/index.md](../kb/gotchas/index.md) with a new `G-nn` ID, and put
the detail in the owning topic file. Do not duplicate the detail into both.

## Style

- One topic per file. If a file needs a table of contents, split it.
- Link to the topic file, not to a heading inside it, unless the heading is stable.
- Keep [../kb/index.md](../kb/index.md) accurate. An index that routes to a file that does not
  answer the question is worse than no index.
- English only.
