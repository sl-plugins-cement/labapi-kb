# AdminToys

**Source:** `LabApi/Features/Wrappers/AdminToys`; techniques calibrated in the metarepo's
`toy-tricks-demo` reference gallery
**Verified:** SCP:SL 14.2.7 / 2026-09-20
**Confidence:** in-game-verified (measurements), source-read (API surface)

AdminToys are the native primitive/light/text/waypoint objects. They are the supported way to
build geometry and effects without a client mod, and they replicate to every client.

## Text sizing is a measured formula, not a guess

A world `TextToy`'s rendered width follows:

```
width = DisplaySize.x × scale × 0.05
```

Measured in-game. Use it to place text at a real physical size instead of tuning by eye —
eyeballed text is correct at one viewing distance and wrong everywhere else.

## Shear: get real 3D, not a flat fake

A primitive can be sheared by decomposing the target transform with a **3×3 SVD** and driving
the toy from the result. This yields true parallelepipeds and needle/shard forms that stay
sharp from every viewing angle.

The cheap alternative — a 2D parallelogram — looks correct from one direction and collapses
when the player walks around it. If the shape is meant to be seen from multiple angles, do
the full decomposition.

## Translucency and bloom

Translucent HDR materials combined with a `LightSourceToy` produce controllable bloom. The
light is a separate toy; the glow does not come from the primitive's material alone.

## Motion

`WaypointToy` supports native relative positioning, so a moving waypoint can carry child toys,
a first-person-controlled dummy, and real pickups as a rigid group. This is the supported way
to move a composed object; re-sending absolute positions for each part every tick is both
heavier and visibly desynchronised.

## Budget

Every toy is replicated state. Toy count and update rate are network cost, not just frame
cost. Pose updates for attached models in existing metarepo plugins run at a deliberate
**10 Hz** cap — treat that as the working ceiling for continuous updates rather than
per-frame, and prefer a moving parent over many independently moved children.

## Related

- The `toy-tricks-demo` plugin in the metarepo is the copyable worked example for all of the
  above; its README holds the rules in copyable form.
