# 01 Vision and requirements

Status: draft 0.2 (revised from the initial requirements v0.1, 2026-11-06)

## 1. Vision

Create immersive acoustic environments (soundscapes) rather than playing
recordings. The system should give the impression of one coherent place, not a
list of sounds.

Guiding sentence: *Don't play sounds, build worlds.*

## 2. Principles

- Modular, graph-based configuration
- Real-time audio rendering
- Random and event-driven audio events
- Configuration (editor) is separate from playback (player)
- Desktop/browser for authoring, Android phone for playback
- The scene description is the source of truth (see ADR 0003)

## 3. Functional requirements

### 3.1 Audio sources
Multiple independent sources run at the same time. Source types:
audio files (WAV, OGG, ...), loops, randomly triggered one-shot events,
synthesized sounds, generated noise.

Per-source parameters: volume, playback rate, start delay, repeat interval,
random variation, stereo/room position, filters, effect assignment.

### 3.2 Filters and effects
Each source may have its own effect chain, freely ordered:
low-pass, high-pass, band-pass, equalizer, gain, reverb, delay, stereo
panning, modulation.

### 3.3 Spatial properties
- Position (X/Y, optional Z), distance to the listener, left/right position
- Volume and frequency content depend on distance
- Direct sound versus reverb share
- Membership in a virtual room or reverb zone
- A simplified model (gain, low-pass, reverb send, pan) is sufficient. Full
  physical room acoustics is explicitly out of scope initially.

### 3.4 Reverb and room acoustics
- Multiple rooms/reverb zones
- Later: user-supplied impulse responses (caves, tunnels, halls, churches,
  underground spaces)

### 3.5 Random and event-driven playback
Parameters: min/max interval, probabilities, random parameter deviation,
event groups, dependencies between events, optional temporal patterns.
Examples: drops at random intervals, bursts of several drops, rare loud events,
different drop types, slowly changing water flow intensity, random variation
of volume and position.

Playback must feel natural and varied without obvious repetition.
Randomness must be **reproducible by seed** (debugging, sharing).

### 3.6 Graph editor
Every node is a source, an effect, a control function, or an output.
Connections define signal flow or control dependency (typed, see architecture).

Editor features: add/remove/move/connect nodes, edit parameters, group and
reuse subgraphs, save/load, play directly in the editor, change parameters
while playing.

Node types: Sample, Loop, Noise Generator, Event Generator; Gain, Filter,
Equalizer, Reverb, Delay, Stereo Panner; Randomizer, Timer, Event Trigger,
Parameter Modulator; Audio Bus, Master Output.

### 3.7 Editor and player
**Editor:** graphical configuration, import of own audio files, all
parameters editable, real-time preview, save/export.

**Player (Android):** import and load soundscapes, start/stop, volume,
sleep timer (with fade-out), playback with screen off, low energy use,
no internet required. Optionally a few parameters adjustable on the phone.
Graphical editing in the player is out of scope.

### 3.8 Exchange format
A documented, platform-independent, JSON-based scene format shared by editor
and player (see 03-scene-format.md). Audio files can be embedded or referenced
by relative path; the preferred distribution form is a single bundle file.

## 4. Quality requirements

### 4.1 Sound quality
- No audible clicks or artifacts on fades
- Gapless playback where intended
- No obviously repetitive patterns (verified by long listening runs, see 4.4)
- Natural transitions between events
- No level jumps, no clipping

### 4.2 Robustness (Android)
- Defined behavior on phone calls, other audio apps, and headphone removal
  (pause or fade; never blast through the speaker unexpectedly)
- Sleep timer ends with a smooth fade-out

### 4.3 Usability
- Finished soundscapes are trivial to operate; complexity stays in the editor
- Parameters are understandable and documented
- The graph stays readable for larger scenes

### 4.4 Testability
- Reference scenes (sea, cave, forest) serve as regression material
- Each reference scene gets a one-hour listening run before a version is
  declared done
- Seeded playback makes problems reproducible

### 4.5 Portability
- Platform-independent scene format, editor/player separation
- No unnecessary dependence on proprietary services
- Other players can be written later

## 5. Operation and privacy
Open source preferred; no accounts, no ads, no tracking; offline operation;
local storage; own audio files usable locally; no mandatory cloud.

## 6. Asset licensing
- Samples: self-created or CC0 preferred (noise can be synthesized, which
  avoids the question entirely)
- CC-BY allowed only with attribution stored in scene metadata
- No NC licenses
- Impulse responses follow the same rules

## 7. Out of scope (initially)
Physical simulation of sound propagation; Dolby Atmos production; VR/AR;
head tracking; multichannel speaker setups; automatic generation of realistic
environmental recordings; cloud sync; iOS player.

## 8. Long-term options
Head tracking, listener movement, multiple connected rooms, VR/AR, extended
room acoustics, multichannel/Atmos-compatible output. Not prerequisites.
