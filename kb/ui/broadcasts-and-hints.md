# Broadcasts and hints

**Source:** native `Broadcast.Messages` / `BroadcastAssigner`; HintServiceMeow;
metarepo hint-provider template
**Verified:** SCP:SL 14.2.7 / 2026-09-20
**Confidence:** in-game-verified

Three mechanisms put text on a player's screen, and they fail differently.

## Broadcasts

The client keeps a real FIFO: `Broadcast.Messages`, drained by a single `BroadcastAssigner`.
Broadcasts therefore **queue** rather than fight each other, which makes them the right
choice for short, sequential, one-shot notices — join cards, round announcements.

**G-10 — `shouldClearPrevious` wipes other plugins' messages.** The flag empties the whole
shared queue, not just your own entries. Another plugin's pending announcement disappears
with no trace. Never pass it. If you need to replace your own message, let the queue drain.

## Hints

Native hints overwrite each other: the newest wins and there is no tagging or removal by ID.

**G-11 — a native hint cannot be removed.** It expires on its own timer. Any deliberate
vanilla-hint fallback must be short-lived and throttled, because you cannot take it back and
you cannot coexist with another plugin doing the same thing.

## HintServiceMeow (HSM)

HSM is the mechanism for persistent or frequently refreshed text. It composes overlays across
plugins and supports removal, which native hints do not.

Rules that hold in practice:

- Depend on a `IHintDisplayProvider` abstraction, not on HSM types directly, so a server
  without HSM degrades to a null provider instead of failing to load.
- Use **stable** hint IDs and groups. Replacement and removal key off them; a generated ID
  per update leaks hints that nothing can clear.
- Layout traps (alignment, wrapping) are real and measured — verify against real game
  backgrounds and then in-game. A preview render does not establish in-game appearance.

## Choosing

| Need | Use |
| --- | --- |
| One-shot notice, ordering matters | Broadcast (never `shouldClearPrevious`) |
| Persistent HUD, refreshed state, composed with other plugins | HSM with stable IDs |
| Brief fallback where HSM is absent | Native hint, short and throttled |

## Related

- [gotchas/index.md](../gotchas/index.md) — G-10, G-11
