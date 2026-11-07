# 02 Architecture

Status: draft 0.2

## 1. Core idea

The **scene description** is the source of truth. The graph in the editor is
only a way to view and edit it. A player *interprets* the description and drives
an audio engine. Editor, player, and audio engine are therefore replaceable
(ADR 0003).

```
 Editor  --writes-->  Scene bundle (JSON + audio)  --read by-->  Player
                                                                   |
                                                                   v
                                                            Audio engine
```

## 2. Components

| Component | Responsibility |
|---|---|
| Audio sources | Files, loops, generated noise, synthesized sounds |
| Signal processing | Filters, EQ, gain, delay, reverb, panning, modulation |
| Event control | Timers, randomizers, triggers, event groups, seeded PRNG |
| Spatial model | Maps position/distance/room to gain, low-pass, reverb send, pan |
| Mix and output | Buses, master, limiter, fades, sleep timer |

The internal audio architecture must not depend on the graphical editor or on
the exchange format's storage details.

## 3. Typed ports

One graph mixes three kinds of connections. To keep the editor sane, every
port has a type, and only matching types connect.

| Port type | Carries | Example |
|---|---|---|
| `audio` | Continuous signal | Noise -> Filter -> Reverb -> Master |
| `event` | Discrete triggers | Timer -> Sample (play) |
| `control` | Numeric parameter values over time | Slow LFO -> Filter cutoff |

Allowed connections: audio->audio, event->event input, control->parameter.
Anything else is rejected by the editor and by the scene validator.

## 4. Event scheduling is separate from the audio graph

The audio graph renders sound; a scheduler decides *when* things happen.

- The scheduler uses a seeded pseudo-random generator, so a seed reproduces a run.
- Events are scheduled with a look-ahead against the audio clock, not against
  UI timers, so timing does not depend on screen state.
- Look-ahead must be long enough to survive throttled timers when the screen is
  off. The needed value is measured in the spike (spike 0.0).
- Slow parameter drift (hours-long scenes) is a first-class feature, so a
  night-long scene does not sound like a short loop.

## 4a. Randomness

- Every consumer of randomness (event scheduler, noise generator, randomizer,
  random modulation, random start offsets of loops) owns an **independent
  stream**.
- A stream's seed is derived from the scene seed and the node's path, which
  includes the ids of all enclosing template instances. Two instances of the same
  template therefore sound different, while one scene stays stable across runs.
- This matters beyond reproducibility: two nodes producing *identical* noise
  are fully correlated. Their levels add coherently (about +6 dB instead of
  about +3 dB for uncorrelated sources) and panning/delay produces comb-filter
  coloration. The same applies to identical loops starting in phase, so start
  offsets are drawn from the streams too.
- Correlation is a hazard by default, not a prohibition: the tools warn but never
  forbid identical seeds. Deliberate use (phasing, beating, flanging-like
  effects, exact duplicates) is a legitimate creative technique.
- The format specifies the PRNG algorithm and the seed-derivation hash, so every
  player produces the same streams from the same seed (see ADR 0009).
- Reproducibility means: same scene, same seed, same inputs gives the same
  event sequence in **scene time** (not wall-clock time). Live parameter
  changes and interruptions can legitimately alter a run.
- Seeds are user-visible and configurable: a scene seed is generated when a
  project is created and can be replaced by a typed one. Groups and nodes may
  carry their own explicit seed (a **seed anchor**); see 03-scene-format.md
  section 10 and editor concepts section 6a.
- The scene declares a **seed mode**: `fixed` (the player uses the scene seed,
  every run is identical) or `fresh` (the player draws a new seed at every
  start). Proposed default: `fresh`, so each night differs.
- The player may show the current seed, offer "reroll", and let the listener
  keep a run they liked.

## 5. Simplified spatial model

For each source the model derives:

- gain from distance
- low-pass cutoff from distance (distant = duller)
- dry/reverb mix from distance and room
- pan from horizontal position

Z position and binaural rendering come later (roadmap 0.5). Binaural rendering
only helps with headphones, so it must be a per-scene option.

## 6. Technology candidates (to be decided after spike 0.0)

All capability statements below are **to be verified** in prototypes.

| Candidate | Strengths | Concerns |
|---|---|---|
| Web Audio API (browser, PWA/WebView on Android) | Filters, delay, panner (HRTF), convolution reverb, worklets for noise/synthesis are built in; one engine for editor preview and player; offline PWA fits privacy goals | Reliability of screen-off background playback and timer throttling on Android |
| Godot | Open source, Android + desktop export, audio buses and effects, GraphEdit | No built-in convolution reverb as far as known; a full engine is heavy for an energy-efficient player |
| Native Android audio (e.g. Oboe, miniaudio) | Best control over background playback and power | Every node must be implemented a second time; browser preview and player may sound different |

Working assumption: **Web Audio for both editor and player**, one codebase,
one sound. This is only confirmed if the spike passes (ADR 0002).

## 7. Editor

Browser-based. A node-graph library (e.g. LiteGraph.js or Rete.js, both MIT as
far as known) or a custom SVG implementation. The editor's only output is a
scene bundle that validates against the format specification. The editor comes
after the player in the roadmap (ADR 0006).

## 8. Packaging

A scene is a single bundle file (a ZIP containing `scene.json` and audio assets).
This resolves "embedded versus referenced" and makes scenes easy to share.
Proposed extension: `.phonotope`.

## 9. Versioning

The scene format carries a `formatVersion` from day one. See 03-scene-format.md
for compatibility rules.
