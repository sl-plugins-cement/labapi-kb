# Index

One line per topic. Find your topic, open that file, stop reading here.

Entries marked _(not written)_ have no content yet — go to the source tree directly and
consider writing the entry afterwards.

## Platform

| Topic | File |
| --- | --- |
| Plugin base class, metadata, Enable/Disable, load priority, transparent mods | [platform/plugin-lifecycle.md](platform/plugin-lifecycle.md) |
| Events: naming, cancellation, registration, static-handler hazards | [platform/events.md](platform/events.md) |
| Config loading and saving, plugin directories, native config paths | [platform/configs-and-paths.md](platform/configs-and-paths.md) |
| Remote Admin and player console commands, permission checks | [platform/commands-and-permissions.md](platform/commands-and-permissions.md) |
| Logging and console output | _(not written)_ — `LabApi/Features/Console/Logger.cs` |

## Players

| Topic | File |
| --- | --- |
| Player wrapper lifetime, ReferenceHub, lookup, dummies | _(not written)_ — `LabApi/Features/Wrappers/Players` |
| Roles, spawning, role change ordering | _(not written)_ |
| Inventory and items | _(not written)_ — `LabApi/Features/Wrappers/Items` |
| Damage, death, ragdolls | _(not written)_ |

## World

| Topic | File |
| --- | --- |
| AdminToys: primitives, lights, text, waypoints, shear rigs, replication limits | [world/admin-toys.md](world/admin-toys.md) |
| Rooms, zones, doors, elevators | _(not written)_ — `LabApi/Features/Wrappers/Facility` |
| ProjectMER schematics | _(not written)_ |

## UI and player-facing output

| Topic | File |
| --- | --- |
| Server-Specific Settings: ID blocks, PlayerPrefs keys, join-time ordering | [ui/server-specific-settings.md](ui/server-specific-settings.md) |
| Broadcasts and hints, including HintServiceMeow | [ui/broadcasts-and-hints.md](ui/broadcasts-and-hints.md) |
| Audio playback | _(not written)_ — `LabApi/Features/Audio` |

## Interop

| Topic | File |
| --- | --- |
| Reading data owned by another plugin | [interop/reading-other-plugins.md](interop/reading-other-plugins.md) |
| Harmony: when it is justified, patch hygiene | _(not written)_ |
| EXILED to LabAPI mapping | _(not written)_ |

## Cross-cutting

| Topic | File |
| --- | --- |
| **All gotchas, by symptom** | [gotchas/index.md](gotchas/index.md) |
| Minimal plugin that compiles and loads | [patterns/minimal-plugin.md](patterns/minimal-plugin.md) |
