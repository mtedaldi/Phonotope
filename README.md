# Phonotope

> **Don't play sounds, build worlds.**
> Build your own soundscapes: a modular, node-based ambient sound generator.

**Status: pre-alpha, documentation only.** There is no code yet. The current
work is the design: requirements, architecture, a draft scene format, and a
feasibility spike for Android background playback.

## What is this?

Most ambient and relaxation apps play back pre-recorded loops. Phonotope aims
to *generate* a coherent acoustic environment instead:

- independent sound sources (loops, samples, synthesized noise, random events)
- a per-source effect chain (filters, reverb, delay, panning, modulation)
- a simple spatial model (distance, position, reverb zones)
- random and event-driven behavior that avoids audible repetition
- a platform-independent scene description (JSON) that any player can interpret

Reference example: a cave with water drops at different distances, a shared
cave reverb, the occasional splash, and slowly changing intensity. A near drop
sounds clear and direct; a distant one is quieter, duller, and more reverberant.

Planned split: a **desktop/browser editor** for building scenes and an
**Android player** for playback (screen off, sleep timer, fully offline).

## Why "Phonotope"?

A *biotope* is a habitat; a *phonotope* is a habitat made of sound
(Greek *phone*, sound + *topos*, place). The term is used in philosophy
(Peter Sloterdijk) and in music: the composer Rolf Wallin has used it since
2001 for pieces in which sounds populate a space like creatures and interact
according to rules. This project borrows the idea and the word, with respect.

Name variants we reserve the right to enjoy: Phontastic, Phonotopia,
Phonotope Lab.

## Principles

- Open source, no accounts, no ads, no tracking, offline-first, local storage
- The scene description is the source of truth; editor, player, and audio
  engine stay replaceable (see [architecture](docs/02-architecture.md))
- Complexity lives in the editor; finished scenes are simple to play

## Documentation

| Document | Content |
|---|---|
| [docs/01-vision-and-requirements.md](docs/01-vision-and-requirements.md) | Goals, requirements, quality criteria, scope |
| [docs/02-architecture.md](docs/02-architecture.md) | Layers, port types, event scheduling, technology candidates |
| [docs/03-scene-format.md](docs/03-scene-format.md) | Draft specification of the JSON scene format |
| [docs/04-roadmap.md](docs/04-roadmap.md) | Versions with exit criteria |
| [docs/05-editor-concepts.md](docs/05-editor-concepts.md) | Templates, exposed parameters, macros, dashboard |
| [docs/spike-0.0-background-playback.md](docs/spike-0.0-background-playback.md) | Test plan for Android background playback |
| [docs/decisions/](docs/decisions/) | Architecture decision records (the *why*) |

## Licensing

- **Code:** [MIT](LICENSE)
- **Audio assets** (samples, impulse responses): self-created or CC0 preferred.
  CC-BY only with attribution carried in the scene metadata. No NC licenses.
  Every asset records its source and license (see the scene format).

## AI-assisted development

This project is developed with the help of AI. The initial requirements
document was drafted by the maintainer; the structure, revisions, and written
documentation in this repository were prepared in conversation with Claude
(Anthropic). Design decisions are made and reviewed by the human maintainer.
Any code added later will be marked in the same way. AI output may contain
errors; claims about technology capabilities are flagged "to be verified"
until tested in a spike.
