# Minimal plugin

**Source:** `LabApi.Examples/Commands/CommandsPlugin/CommandsPlugin.cs` and siblings
**Verified:** LabAPI 1.1.7 / SCP:SL 14.2.7 / 2026-09-20
**Confidence:** source-read

The smallest plugin that loads. Copy, rename, then add to `Enable()`.

```csharp
using System;
using LabApi.Features;
using LabApi.Loader.Features.Plugins;

namespace MyPlugin;

public class MyPlugin : Plugin
{
    public override string Name => "MyPlugin";

    public override string Description => "One sentence on what it does.";

    public override string Author => "...";

    public override Version RequiredApiVersion { get; } = new Version(LabApiProperties.CompiledVersion);

    public override void Enable()
    {
    }

    public override void Disable()
    {
    }
}
```

`LabApiProperties.CompiledVersion` pins `RequiredApiVersion` to the LabAPI you compiled
against, which is almost always what you want. Hard-code a lower version only when you have
actually checked the plugin works against it.

Set an assembly version, or every build reports `0.0.0.0` (G-06).

## With a config

```csharp
public class MyPlugin : Plugin<MyConfig>
{
    // ... same metadata ...

    public override void Enable()
    {
        // Config is already loaded here. Validate it - a malformed file
        // silently became defaults (G-04).
    }
}

public class MyConfig
{
    public bool Enabled { get; set; } = true;
}
```

`TConfig` must be `class, new()`. The file defaults to `config.yml`; override
`ConfigFileName` to change it. `SaveConfig()` writes the current object back.

## Registering events

```csharp
public override void Enable()
{
    PlayerEvents.Joined += OnJoined;
}

public override void Disable()
{
    PlayerEvents.Joined -= OnJoined;   // not optional - see G-01
}

private void OnJoined(PlayerJoinedEventArgs ev)
{
}
```

For more than a handful of handlers, derive from `CustomEventHandlers` and register the
instance with `CustomHandlersManager.RegisterEventsHandler` / `UnregisterEventsHandler`, so
registration and unregistration cannot drift apart.

## A command

```csharp
[CommandHandler(typeof(FunParentCommand))]
public class MyCommand : ICommand
{
    public string Command { get; } = "mycommand";
    public string[] Aliases { get; } = new[] { "mc" };
    public string Description { get; } = "...";

    public bool Execute(ArraySegment<string> arguments, ICommandSender sender, out string response)
    {
        if (!sender.HasPermission("myplugin.use"))
        {
            response = "No permission.";
            return false;
        }

        if (!Player.TryGet(sender, out Player? player))
        {
            response = "You must be a player to use this command.";
            return false;
        }

        response = "Done.";
        return true;
    }
}
```

Check permission on the **sender**, then resolve the player — a sender is not necessarily a
player. See [../platform/commands-and-permissions.md](../platform/commands-and-permissions.md).

## Build target

net48, x64, C# 12 — matching LabAPI 1.1.7's own project settings. Reference the LabAPI
assembly and the game's managed assemblies; do not copy them into the repository.
