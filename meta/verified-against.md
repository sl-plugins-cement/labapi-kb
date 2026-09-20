# Verified against

The baseline behind every entry that does not state its own.

| | |
| --- | --- |
| SCP:SL game version | 14.2.7 |
| Server build | deploy-befaf3d9, built 2026-05-21 09:02:02Z |
| LabAPI | 1.1.7 |
| Target framework | net48, x64, C# 12 |
| Last full review | 2026-09-20 |

## What to do after a game update

Entries do not re-validate themselves. When the server build changes:

1. Update this file with the new version, and set **Last full review** only once a review has
   actually happened.
2. Re-check the entries whose **Confidence** is `in-game-verified` — those describe observed
   behavior and are the ones an update can silently invalidate.
3. Re-check any entry whose cited source path no longer exists or whose cited symbol moved.
   A dead citation means the claim is unverifiable, and an unverifiable claim is worse than
   a missing one.
4. Correct entries in place. Do not append "as of 14.2.7 this was …" — history belongs in the
   commit log, not in the entry.

## Reconstructing the evidence tree

Entries cite paths in the LabAPI source and in decompiled dedicated-server assemblies. Neither
is vendored here. To make citations resolvable on a new machine, clone LabAPI at the version
in the table above and decompile the local dedicated-server managed assemblies; the plugin
metarepo's `.references/scripts/` holds the refresh scripts that produce the tree these paths
assume.
