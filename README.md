# LabAPI knowledge base

Agent-facing knowledge for building SCP: Secret Laboratory server plugins on LabAPI.

This repository is **not** a LabAPI mirror and not a copy of the game's decompiled source.
Those are reconstructible on any machine and add nothing an agent cannot get by reading them.
What lives here is the part that is *not* derivable from a first reading of the source:

- **Gotchas** — behavior that contradicts the obvious reading of the API.
- **Lookup routes** — where the authoritative answer for a topic lives, and in what order to check.
- **Verified constants and formulas** — measured in-game, not inferred.
- **Minimal working patterns** — the smallest thing that compiles and behaves correctly.

Every claim carries its source path, the build it was verified against, and whether it was
read from source or observed in-game. See [meta/contributing.md](meta/contributing.md).

## Start here

- [kb/index.md](kb/index.md) — the router. One line per topic; the only file worth reading in full.
- [AGENTS.md](AGENTS.md) — how an agent should use and extend this repository.
- [meta/verified-against.md](meta/verified-against.md) — the game build and LabAPI version behind the current entries.

## Scope

The full server-plugin platform, not just LabAPI's own surface: the loader and wrapper API,
native dedicated-server behavior reached through it, and the surrounding ecosystem that real
plugins depend on (Server-Specific Settings, HintServiceMeow, broadcasts, AdminToys, audio,
ProjectMER, EXILED interop).

Out of scope: product documentation for individual plugins, deployment and operations runbooks,
machine-local paths, and historical accounts of bugs that have already been fixed.
