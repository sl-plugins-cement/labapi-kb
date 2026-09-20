# Server-Specific Settings (SSS)

**Source:** native `ServerSpecificSettings` / `DefinedSettings`; behavior established by the
`ServerKeybinds` registry (API 5) in the plugin metarepo
**Verified:** SCP:SL 14.2.7 / 2026-09-20
**Confidence:** in-game-verified

SSS is the native per-player options panel: keybinds, dropdowns, sliders, two-button toggles.
It is the right surface for any persistent player preference. It is also the surface with the
most destructive failure modes, because the state lives on the *client*.

## One process-wide registry

`DefinedSettings` is process-wide. Two plugins that assign it independently overwrite each
other and the loser's settings vanish with no error.

Every plugin must claim a fixed 1000-ID block and register through the shared registry, which
performs one additive merge and owns menu order. Never touch `DefinedSettings` directly.
Blocks declare a category (`Gameplay`, `Display`, `Announcements`, `Tools`); uncategorised
blocks fall into `Other`, last.

Re-categorising a setting is safe — it changes no setting IDs.

## G-07 — changing a setting's type wipes every player's stored value

The client stores each setting under the PlayerPrefs key:

```
SrvSp_<server>_<typeCode>_<settingId>
```

The **type code is part of the key**. Converting a dropdown to a two-button toggle, or any
other type change at the same setting ID, produces a different key. Every player silently
reverts to the default, with no migration path and no server-side signal that it happened.

If a setting's type must change, allocate a **new setting ID** and migrate deliberately.
Treat a released setting's type as immutable.

## G-08 — the server cannot assign a keybind

A keybind default is only a `SuggestedKey`. The player must accept it. Any feature that
assumes "the key is bound because I shipped a default" is broken for every player who never
opened the settings panel.

Design for the unbound state: the feature must be discoverable and, where it matters,
reachable by some other route.

## G-09 — stored settings arrive after the player joins

The client sends its stored SSS responses *after* join completes. Anything due at join time —
a welcome card, an opt-out check, a first-spawn decision — reads a value that has not arrived
yet and gets the default.

The working pattern is to mirror the value server-side (a small keyed file written whenever
the setting changes) and read the mirror at join, treating the live SSS value as the
authority only once it has been received.

## Reading another plugin's setting

`TryGetSettingOfUser` reads a setting defined by a different plugin. The defining plugin owns
the ID; the reader must not define it. This is the supported cross-plugin option pattern —
one definer, many readers.

## Related

- [gotchas/index.md](../gotchas/index.md) — G-07, G-08, G-09
- [../interop/reading-other-plugins.md](../interop/reading-other-plugins.md)
