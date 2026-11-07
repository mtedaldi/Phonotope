# 04 Roadmap

Each version is independently usable and has exit criteria.
Order differs from the initial requirements: the **format and player come
before the editor** (ADR 0006), and a feasibility spike comes first.

## Version 0.0: spike

Goal: decide the technology with evidence.
Content: [Spike 0.0](spike-0.0-background-playback.md) (Android background
playback, timer behavior, battery).
Exit: written results and a decision record for the player technology.

## Version 0.1: format and minimal player

Goal: play a hand-written scene.
- Scene format v0.1 draft and a validator
- Player core: multiple sources, loops, per-source gain, master output
- Generated noise
- Simple UI: load scene, start/stop, volume
- Reference scene: **sea** (several combined noise/loop components)

Exit: sea scene plays for one hour without clicks or level jumps.

## Version 0.2: spatial soundscapes and random events

Goal: the cave.
- Per-source filters, reverb zones, delay, panning
- Distance model (gain, low-pass, reverb share)
- Seeded random events, groups, bursts, rare loud events, slow drift
- Reference scene: **cave**; first impulse-response experiments

Exit: cave scene runs for one hour without audible repetition patterns;
same seed reproduces the same run.

## Version 0.3: Android player

Goal: use it for real, phone in pocket.
- Packaging/import of bundles
- Playback with screen off, sleep timer with fade-out
- Audio focus handling (calls, headphone removal)
- Dashboard in the player: sliders for the exposed parameters of a scene
- Reference scene: **forest** (birds, crickets)

Exit: a full night run on a phone with measured battery use.

## Version 0.4: browser editor

Goal: author scenes graphically.
- Node graph with typed ports, parameter editing, save/load
- Live preview using the same engine as the player
- Subgraph grouping and reuse
- Templates (copy with provenance), exposed parameters, macros, dashboard editing
  (see [editor concepts](05-editor-concepts.md))
- Editor writes only validated scenes

Exit: all three reference scenes can be rebuilt from scratch in the editor.

## Version 0.5: binaural audio

- Per-scene binaural option (headphones)
- Compare stereo panning with binaural rendering
- Investigate HRTF choices (the browser's generic HRTF may suffice)

May be pulled forward if it proves cheap with the chosen engine.

## Later (no commitments)

Head tracking, listener position and movement, connected rooms, VR/AR,
advanced room acoustics, multichannel/Atmos-compatible output.
