# Plugin lifecycle

**Source:** `LabApi/Loader/Features/Plugins/Plugin.cs`, `Plugin{TConfig}.cs`
**Verified:** LabAPI 1.1.7 / SCP:SL 14.2.7 / 2026-09-20
**Confidence:** source-read

## The base class

A plugin subclasses `LabApi.Loader.Features.Plugins.Plugin`, or `Plugin<TConfig>` when it
needs a config file.

Abstract — you must supply all five:

- `string Name`
- `string Description`
- `string Author`
- `Version RequiredApiVersion`
- `void Enable()` and `void Disable()`

Virtual, with defaults worth knowing:

- `Version Version` defaults to `GetType().Assembly.GetName().Version`. If you never set an
  assembly version, every build reports `0.0.0.0` and version-gated behavior in other plugins
  cannot distinguish your releases.
- `LoadPriority Priority` defaults to `LoadPriority.Medium`. A plugin that others depend on
  at load time must raise this; relying on alphabetical or filesystem order is not stable.
- `bool IsTransparent` defaults to `false`. Set it to `true` only for a plugin that does not
  change gameplay — it is a truthfulness claim to players and to the verified-server checks,
  not a convenience flag.
- `void LoadConfigs()` — see [configs-and-paths.md](configs-and-paths.md).

## Enable and Disable

`Enable()` registers; `Disable()` must unregister everything `Enable()` registered. This is
not merely tidy: LabAPI can reload plugins in a live process, so anything left subscribed
survives into the next load and runs twice.

The asymmetric cases that actually leak:

- Static event handlers (see [events.md](events.md)).
- Coroutines started through MEC — kill the handle, do not assume round end clears it.
- Harmony patches — unpatch with the same instance id.
- Anything registered into another plugin's shared registry. Releasing a lease is your job.

Treat `Disable()` as "return the process to the state it was in before `Enable()`", and assume
it will be followed by another `Enable()` in the same process.

## Load order and dependencies

Assemblies load from the plugins directory; shared libraries that are not themselves plugins
belong in the dependencies directory instead. See [configs-and-paths.md](configs-and-paths.md)
for both locations.

A plugin that hard-depends on another should state a minimum API version of that dependency
and fail loudly in `Enable()` when it is absent, rather than throwing a `TypeLoadException`
at first use. A missing dependency assembly surfaces as a load failure with no useful
plugin-level message otherwise.

## Related

- [gotchas/index.md](../gotchas/index.md) — G-01, G-06
