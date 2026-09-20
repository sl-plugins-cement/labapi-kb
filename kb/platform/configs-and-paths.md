# Configs and paths

**Source:** `LabApi/Loader/Features/Paths/PathManager.cs`,
`LabApi/Loader/Features/Plugins/Plugin{TConfig}.cs`, `LabApi/Loader/Features/Configuration`
**Verified:** LabAPI 1.1.7 / SCP:SL 14.2.7 / 2026-09-20
**Confidence:** source-read

## Directories

`PathManager` owns every directory; do not hard-code paths. It creates the tree in its static
constructor, so the directories exist by the time any plugin runs.

```
<AppData>/SCP Secret Laboratory/LabAPI/
    plugins/         plugin assemblies
    dependencies/    shared libraries that are not themselves plugins
    configs/         plugin configuration
```

Exposed as `PathManager.AppData`, `.SecretLab`, `.LabApi`, `.Plugins`, `.Dependencies`,
`.Configs`, all `DirectoryInfo`.

A shared library placed in `plugins/` instead of `dependencies/` will be scanned for plugin
types. Put non-plugin assemblies in `dependencies/`.

## Native config paths

`PathManager` also resolves the dedicated server's own files, which is the supported way to
reach them:

| Property | Points at |
| --- | --- |
| `RAConfigPath` | Remote Admin roles config |
| `GameplayConfigPath` | `config_gameplay` |
| `SharingConfigPath` | `config_sharing` |
| `MutesConfigPath` | shared mutes file |
| `UserIdBansPath`, `IpBansPath` | ban files |
| `WhitelistConfigPath` | `UserIDWhitelist.txt` |
| `ReservedSlotsConfigPath` | `UserIDReservedSlots.txt` |

These are shared, natively-owned files. The game and other plugins write them too. Read them
fresh rather than caching at load, and never rewrite one wholesale without preserving entries
you did not author.

## Plugin config

`Plugin<TConfig>` requires `TConfig : class, new()`, exposes `TConfig Config`, and reads
`ConfigFileName` (default `config.yml`). `SaveConfig()` writes the current object back.

**G-04 — a malformed config file is not an error.** `Plugin<TConfig>.LoadConfigs()` calls
`TryLoadConfig`, and on failure logs `"Failed to load the configuration file, using default
values."` at warning level and substitutes `new TConfig()`. The plugin then enables normally.
An operator who mistypes a YAML key gets a fully running plugin on default settings, with one
warning line buried in startup output.

If correct configuration is load-bearing — a whitelist, an ID block, a credential — validate
it in `Enable()` and refuse to start, rather than trusting that a loaded config was a parsed
config.

## Related

- [gotchas/index.md](../gotchas/index.md) — G-04, G-05
