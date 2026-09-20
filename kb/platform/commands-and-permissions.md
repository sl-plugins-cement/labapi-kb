# Commands and permissions

**Source:** `LabApi/Features/Permissions/**`, `LabApi/Loader/Features/Commands/**`
**Verified:** LabAPI 1.1.7 / SCP:SL 14.2.7 / 2026-09-20
**Confidence:** source-read

## Choosing a surface

SCP:SL has no custom client UI. Three surfaces exist, and picking the wrong one is the most
common design error in a new plugin:

- **Remote Admin** — staff actions. Structured verbs, permission-gated, output back to the RA
  console. Use it for anything an administrator does.
- **Player console** — simple self-service player commands (`.something`). No arguments worth
  parsing, no state a player could abuse.
- **Server-Specific Settings** — player *options*, not commands. If the player is expressing a
  persistent preference, it belongs here, not behind a console verb.
  See [../ui/server-specific-settings.md](../ui/server-specific-settings.md).

## Permission checks

`PermissionsExtensions` extends `ICommandSender`:

| Method | Semantics |
| --- | --- |
| `HasPermission(string)` | one permission |
| `HasPermissions(params string[])` | **all** of them |
| `HasAnyPermission(params string[])` | **any** of them |
| `GetPermissions()` | flat list |
| `GetPermissionsByProvider()` | grouped by provider type |
| `AddPermissions` / `RemovePermissions` | mutate |

**G-05 — `HasPermissions` is all-of, `HasAnyPermission` is any-of.** The plural `s` on the
first is the only thing distinguishing them, and the wrong one silently over- or under-grants.
Pass a single permission to `HasPermission` and the ambiguity disappears.

Permissions resolve through `PermissionsManager` over registered `IPermissionsProvider`s;
`DefaultPermissionsProvider` reads LabAPI's own group file. Because providers are pluggable,
a sender can hold a permission that appears in no file you can find — query the API, never
parse a permissions file yourself to decide access.

## Gating rules

Check permission on the `ICommandSender`, not on a resolved `Player`. A command can arrive
from the server console or another plugin, where there is no player at all; code that assumes
a player either crashes or, worse, treats the sender as unprivileged and hides an action the
console is entitled to perform.

Fail closed. An unknown sender type, a missing provider, or an unresolvable target is a
refusal, not a default-allow.

## Related

- [gotchas/index.md](../gotchas/index.md) — G-05
