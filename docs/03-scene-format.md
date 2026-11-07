# 03 Scene format (draft)

Status: **draft, not stable.** Expect breaking changes until the first player
(roadmap 0.1) has consumed hand-written scenes.

## 1. Goals

Platform-independent, human-readable, engine-independent, versioned,
validatable. Designed so scenes can be written by hand before an editor exists.

## 2. Bundle

A bundle is a ZIP file (proposed extension `.phonotope`):

- `scene.json`: the scene description
- `assets/`: audio files referenced by the scene

A bare `scene.json` with references to external files is also valid for
development.

## 3. Top-level structure

| Field | Meaning |
|---|---|
| `formatVersion` | Format version, `major.minor` |
| `requiredFeatures` | Optional list of feature flags a player must support (section 12) |
| `meta` | Title, author, description, license of the scene itself |
| `seed` | Scene seed; each random consumer derives its own stream (section 10). If absent, the player draws a fresh seed per start |
| `assets` | List of audio assets with source, license, attribution |
| `rooms` | Reverb zones (algorithmic parameters or an impulse-response asset) |
| `nodes` | Node definitions (id, type, parameters) |
| `connections` | Typed links between node ports |
| `groups` | Named subgraphs with an optional interface, origin, and overrides (section 9) |
| `macros` | One control driving several parameters (section 9) |
| `dashboard` | Controls shown to the user, including in the player (section 9) |
| `ui` | Optional editor data (node positions); players ignore it |

## 4. Illustrative example (shape only, not final)

```json
{
  "formatVersion": "0.1",
  "meta": { "title": "Cave", "license": "CC0-1.0" },
  "seed": 42,
  "assets": [
    { "id": "drop1", "path": "assets/drop1.ogg", "license": "CC0-1.0", "source": "self-recorded" }
  ],
  "rooms": [
    { "id": "cave", "reverb": { "type": "algorithmic", "decay": 4.5 } }
  ],
  "nodes": [
    { "id": "timer1", "type": "timer", "params": { "minInterval": 2.0, "maxInterval": 9.0 } },
    { "id": "drop-near", "type": "sample", "params": { "asset": "drop1", "distance": 1.0, "room": "cave" } },
    { "id": "out", "type": "master-output" }
  ],
  "connections": [
    { "from": "timer1.event", "to": "drop-near.play" },
    { "from": "drop-near.audio", "to": "out.audio" }
  ]
}
```

## 5. Node types (initial catalogue)

- Sources: `sample`, `loop`, `noise`, `event-generator`
- Processing: `gain`, `filter`, `equalizer`, `reverb`, `delay`, `stereo-panner`
- Control: `randomizer`, `timer`, `event-trigger`, `parameter-modulator`
- Outputs: `audio-bus`, `master-output`

Each node type will get its own parameter table (name, type, unit, range,
default, whether it may be modulated). Units are SI where sensible (seconds,
hertz, meters, decibels).

## 6. Compatibility rules (proposed)

- `formatVersion` is `major.minor`.
- A player must reject a scene with an **unknown major** version, with a clear message.
- A player must ignore **unknown optional fields** within a known major version.
- Minor versions only add optional things.
- Breaking changes bump the major version and ship with a documented migration.
- A scene that lists `requiredFeatures` unknown to the player must be rejected with a clear message (section 12).
- The scene validator is part of the project, so editor and players test
  against the same rules.

## 7. Asset metadata

Every asset records `source` and `license`; CC-BY assets also record
`attribution`. Players should be able to list attributions for a loaded scene.

## 8. Open points

- Exact parameter tables per node type
- Impulse-response handling (embedded versus separate asset)
- Whether control signals are sampled or event-based in the data model

## 9. Groups, templates, macros, dashboard (draft)

See [05-editor-concepts.md](05-editor-concepts.md) for the reasoning and
[ADR 0008](decisions/0008-templates-copy-with-provenance.md).

**Groups.** A group contains nodes and connections of its own. It may declare an
`interface`: exposed ports (typed, as in the architecture) and exposed
parameters (`label`, `target`, `range`, `default`, `step`, `unit`, `curve`,
`control`, `level`).

**Origin.** A group created from a template records `origin`:
template id (namespaced, e.g. `phonotope/sea`), version, and `source`
(`builtin`, `user`, `repository`). The graph itself is stored in full (a copy), so the
scene is self-contained. Local edits to a copied group are recorded as
`overrides` so an editor can detect conflicts on update.

**Macros.** A macro has one value (with its own range, default, curve) and a list
of targets. Each target names a node parameter and its own mapping (range and
curve). The player evaluates macros; it needs no knowledge of templates.

**Dashboard.** An ordered list of entries, each referencing an exposed
parameter or macro, with optional grouping and a widget type
(slider, range slider, toggle, choice). The player shows exactly the dashboard.

**Open points.** Exact JSON shapes; per-instance seed derivation for repeated
template instances; whether macro curves are named presets only or free-form.

## 10. Randomness

- The format names one PRNG algorithm and one hash for seed derivation, so all
  players derive identical streams. The exact choices are fixed in ADR 0009
  before format 0.1 is frozen.
- Stream seed = hash(scene seed, node path). The node path includes the instance
  ids of enclosing template groups.
- Everything random draws from its own stream: event timing, noise samples,
  random start offsets, randomizer nodes, random modulation.
- Event times are defined in scene time (the audio clock), not wall-clock time.

## 11. Curves

Mapping curves are objects with a `type`, so new types can be added later.

| Type | Meaning | Typical use |
|---|---|---|
| `linear` | straight mapping | most controls |
| `log` / `exp` | logarithmic / exponential across the range | frequencies, times |
| `power` with `exponent` | x raised to a power | gentle non-linearity |
| `db` | control in decibels, converted to gain | levels |

Planned later (not in 0.1): `points`, a list of control points with
interpolation (piecewise linear first, then a monotone cubic spline that does
not overshoot). Free-form splines with overshoot are avoided because a control
must never exceed its range.

## 12. Required features

A scene may list `requiredFeatures` (for example `curve.points`). A player that
does not know every listed feature rejects the scene with a clear message
rather than playing it wrongly. This lets the format grow without silent
misbehavior and without a major version bump for every addition.
