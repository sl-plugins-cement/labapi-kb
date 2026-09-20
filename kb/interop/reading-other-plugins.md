# Reading data owned by another plugin

**Source:** metarepo practice (`StatsSystem` playtime reads, `CustomizableUIMeow`
`PlayerStatsStore` pattern, SSS cross-plugin reads)
**Verified:** SCP:SL 14.2.7 / 2026-09-20
**Confidence:** in-game-verified

Ranked best to worst. Take the highest option that works.

## 1. A public API on the owning plugin

If the owner exposes a query method, call it. This is the only option that survives the
owner's refactors. When you own both sides, add the API rather than reaching in.

## 2. A shared registry with one definer and many readers

For player options, the definer declares the setting and readers use `TryGetSettingOfUser`.
See [../ui/server-specific-settings.md](../ui/server-specific-settings.md). Two plugins
defining the same ID is a conflict, not a fallback.

## 3. Cached reflection

Last resort, for data with no exposed API. The working pattern: resolve the type and member
once, cache the accessor, and degrade to a null/absent result when resolution fails. Never
reflect per-call, and never let a failed lookup throw into gameplay code — the dependency may
simply not be installed on that server.

A reflection-based reader is a standing liability. Record what you reflected against and
re-check it when the target plugin updates.

## Know which plugin actually owns the data

**G-12 — playtime lives in StatsSystem, not XPSystem.** `TotalPlayTime` is a StatsSystem
field. XPSystem stores only `{UserId, XP, Level}` and has no time field at all. Code that
goes looking for playtime in the XP store finds nothing and, depending on how it handles the
miss, either fails or silently treats every player as brand new.

The general lesson: confirm ownership before reflecting. Two plugins with adjacent
responsibilities are not interchangeable sources, and the absence of a field reads the same
as a zero value unless you distinguish them explicitly.

## Related

- [gotchas/index.md](../gotchas/index.md) — G-12
