# Events

**Source:** `LabApi/Events/Handlers/*.EventHandlers.cs`, `LabApi/Events/Arguments/**`,
`LabApi/Events/CustomHandlers/CustomHandlersManager.cs`
**Verified:** LabAPI 1.1.7 / SCP:SL 14.2.7 / 2026-09-20
**Confidence:** source-read

## Shape

371 static events across 15 handler classes (`PlayerEvents`, `ServerEvents`, `WarheadEvents`,
`ObjectiveEvents`, `Scp914Events`, and one class per SCP role). Each is
`public static event LabEventHandler<TArgs>?`, so subscription is a plain `+=`.

368 argument classes live under `LabApi/Events/Arguments/<Area>Events/`. The argument class
name is the event name plus `EventArgs`, so `PlayerEvents.Joined` takes
`PlayerJoinedEventArgs`. Finding an event's data is a filename lookup, not a search.

## The -ing / -ed pairing

Most actions appear twice: `UsingIntercom` / `UsedIntercom`, `PreAuthenticating` /
`PreAuthenticated`. The `-ing` event fires before the action and usually carries mutable or
cancellable arguments; the `-ed` event reports work already done.

The pairing is a naming convention, not a guarantee. Two rules hold as of 1.1.7:

- **No `-ed` event is cancellable.** Zero `*edEventArgs` classes implement `ICancellableEvent`.
  If you need to stop something, you need the `-ing` event; there is no late escape hatch.
- **Not every `-ing` event is cancellable**, and one of them lies about it — see G-02 below.

169 of 368 argument classes implement `ICancellableEvent`. Check the class, do not assume
from the name.

## Cancellation

`ICancellableEvent` has exactly one member, `bool IsAllowed { get; set; }`. Setting it `false`
prevents the action. When several handlers subscribe to the same event, the last one to run
wins, and nothing tells you another plugin already denied it — read `IsAllowed` before
overwriting it to `true`.

**G-02 — `Scp173SnappingEventArgs` has `IsAllowed` but does not implement `ICancellableEvent`.**
It declares `: EventArgs, IPlayerEvent, ITargetEvent` and exposes a working `IsAllowed`
property. Generic code shaped like `if (args is ICancellableEvent c) c.IsAllowed = false;`
silently fails to block an SCP-173 neck snap while blocking everything else it is pointed at.
Set the property directly on the concrete type.
(`LabApi/Events/Arguments/Scp173Events/Scp173SnappingEventArgs.cs`)

## Two registration styles

**Direct subscription** — `PlayerEvents.Joined += OnJoined;` in `Enable()`, `-=` in
`Disable()`. Fine for a handful of handlers.

**Custom handler classes** — derive from `CustomEventHandlers`, then
`CustomHandlersManager.RegisterEventsHandler(instance)` and
`UnregisterEventsHandler(instance)`. Better when a feature owns many events, because
unregistration cannot drift out of sync with registration.

Whichever you pick, the events are **static**. They outlive your plugin instance. A handler
that captures plugin state and is never unsubscribed keeps the old instance alive across a
reload, and both instances then react to the same event.

## Related

- [gotchas/index.md](../gotchas/index.md) — G-01, G-02, G-03
