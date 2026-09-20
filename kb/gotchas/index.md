# Gotchas, by symptom

Behavior that contradicts the obvious reading of the API. Scan the symptom column; the detail
lives in the linked topic file.

**Verified:** LabAPI 1.1.7 / SCP:SL 14.2.7 / 2026-09-20 unless an entry says otherwise.

| ID | Symptom you will actually observe | Cause |
| --- | --- | --- |
| G-01 | Handler fires twice, or old config values reappear, after a plugin reload | LabAPI events are **static** and outlive the plugin instance. `Disable()` did not unsubscribe. [platform/events.md](../platform/events.md) |
| G-02 | Generic "cancel this event" code blocks everything except an SCP-173 neck snap | `Scp173SnappingEventArgs` exposes a working `IsAllowed` but does **not** implement `ICancellableEvent`, so `is ICancellableEvent` skips it. [platform/events.md](../platform/events.md) |
| G-03 | Looking for a way to undo an action from its `-ed` event | No `*edEventArgs` implements `ICancellableEvent`. Cancellation only exists on the `-ing` event. [platform/events.md](../platform/events.md) |
| G-04 | Plugin runs fine but ignores the operator's settings | A malformed config is **not** an error: `Plugin<TConfig>.LoadConfigs()` logs one warning and substitutes defaults. [platform/configs-and-paths.md](../platform/configs-and-paths.md) |
| G-05 | A command is open to more or fewer staff than intended | `HasPermissions` is **all-of**; `HasAnyPermission` is **any-of**. One letter apart. [platform/commands-and-permissions.md](../platform/commands-and-permissions.md) |
| G-06 | Every build of your plugin reports version `0.0.0.0` | `Plugin.Version` defaults to the assembly version, which is `0.0.0.0` if never set. [platform/plugin-lifecycle.md](../platform/plugin-lifecycle.md) |
| G-07 | Every player's saved option silently reset after an update | The client PlayerPrefs key is `SrvSp_<server>_<typeCode>_<settingId>` — the **type is part of the key**, so changing a setting's type orphans the stored value. [ui/server-specific-settings.md](../ui/server-specific-settings.md) |
| G-08 | A feature is unreachable for most players despite a shipped default key | A keybind default is only a `SuggestedKey` the player must accept. The server cannot assign a key. [ui/server-specific-settings.md](../ui/server-specific-settings.md) |
| G-09 | An opt-out or preference reads as default at join, then works later | The client sends stored SSS responses **after** join. Mirror the value server-side and read the mirror at join. [ui/server-specific-settings.md](../ui/server-specific-settings.md) |
| G-10 | Another plugin's announcement vanishes when yours fires | `shouldClearPrevious` empties the **shared** broadcast FIFO, not just your entries. Never pass it. [ui/broadcasts-and-hints.md](../ui/broadcasts-and-hints.md) |
| G-11 | A hint stays on screen and nothing removes it | Native hints have no tag or ID and cannot be removed — they only expire. Use HSM with stable IDs for anything persistent. [ui/broadcasts-and-hints.md](../ui/broadcasts-and-hints.md) |
| G-12 | Every player looks like a brand-new player | Playtime (`TotalPlayTime`) belongs to **StatsSystem**. XPSystem stores only `{UserId, XP, Level}` and has no time field. [interop/reading-other-plugins.md](../interop/reading-other-plugins.md) |
| G-13 | A badge asked to be "hidden" is still visible to players | SCP:SL's silver `(hidden)` marker is still rendered. Invisible to every client means native role color `none`. |
| G-14 | A sheared primitive looks correct from one side and collapses from another | A 2D parallelogram was used where a full 3×3 SVD decomposition is needed. [world/admin-toys.md](../world/admin-toys.md) |

## Adding one

Lead with the symptom, not the cause — that is how an agent arrives here. One row, one cause,
one link. Detail belongs in the topic file. See [../../meta/contributing.md](../../meta/contributing.md).
