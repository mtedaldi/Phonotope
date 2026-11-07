# 05 Editor concepts

Status: ideas and decisions, draft 0.1 (2026-11-06). The editor is built in
roadmap version 0.4; these concepts shape the scene format earlier.

## 1. Problem

A node graph exposes every parameter of every node. That is powerful and
overwhelming. Most users of a "sea" soundscape want two or three controls,
not a noise generator with six filters. The editor must offer both: simple
controls by default, the full graph on demand.

## 2. Templates

A **template** is a prebuilt group (subgraph) plus an interface. Inserting
"Sea" places a noise generator, filters, modulation, and level control, wired
correctly, with sensible defaults. "Wind" can reuse the same generic generator
and filter blocks with a different arrangement and defaults.

A template consists of:
- the internal graph (nodes, connections)
- an **interface**: its exposed audio/event ports and its exposed parameters
- metadata: id, version, name, description, author, license

Templates are an application of the existing "group and reuse subgraphs"
requirement. A user-made group can be turned into a template.

## 3. Exposed parameters

The template author decides which internal parameters are visible outside.
Each exposed parameter defines:

| Field | Meaning |
|---|---|
| `label` | Name shown to the user ("Wave intensity") |
| `target(s)` | Internal node parameter(s) it controls |
| `range`, `default`, `step`, `unit` | Value domain |
| `curve` | Mapping: linear, logarithmic (needed for frequencies), custom |
| `control` | Widget: slider, range slider (two handles), toggle, choice |
| `level` | `basic` (shown by default) or `advanced` (hidden until requested) |

Range sliders matter because many values are intervals, for example the time
between two drops (2 to 9 seconds).

## 4. Macros

One exposed control may drive **several** internal parameters, each with its
own mapping. Example for "Sea": a single *Intensity* control raises noise level,
opens the low-pass cutoff, and increases modulation depth, with different
curves for each. This keeps templates down to a few meaningful controls.

## 5. Dashboard

The dashboard is one view of all exposed parameters of a scene.

- Template parameters appear automatically (basic level).
- The user can **pin** any other parameter to the dashboard, even from a node
  that is not part of a template.
- The dashboard is stored in the scene; the **player shows only the dashboard**.
  This fulfills "optionally change a few parameters on the phone" without a
  second feature.
- Layout can be simple at first (ordered list, grouped per template instance).

## 6. Two levels of view

Every template instance can be shown **collapsed** (a single node with its
sliders) or **opened** (the internal graph). Opening never breaks anything; it
is progressive disclosure, not a mode switch.

## 7. Instantiation: copy with provenance

Decision (ADR 0008): inserting a template **copies** its graph into the scene
and records the origin (`template id`, `version`) on the group.

Consequences:
- A scene is always self-contained and playable without a template library.
- The player never needs to know about templates.
- The editor can compare a group's recorded version with the library and offer
  "A newer version of this template exists. Update?" and the user chooses.
- The user may edit the copied internals freely. Local changes are kept as
  overrides and marked, so an update can warn about conflicts.

## 8. Presets versus templates

- **Template:** a structure (what is wired together).
- **Preset:** a named set of values for one structure ("calm sea", "storm").

Presets are saved parameter sets for a template instance. Both can combine.

## 9. Ideas parking lot (not decided)

- A "dice" button that randomizes within safe ranges, for discovering scenes
- Morphing between two presets over time (the sea slowly turns to storm)
- Sharing templates as files with their own license metadata
- A curve editor for macro mappings
- User-defined dashboard layout and grouping
- Template categories and search in the editor
- Slider values as modulation sources (link a control to a slow LFO)

## 10. Decisions on earlier open questions (2026-11-06)

1. **Built-in templates first, user templates later.** The structure is planned
   for both from the start: a template has one format whether shipped with the
   editor or made by a user (graph + interface + metadata + bundled assets).
   Built-ins are simply templates that ship with the app. Each template
   records its `source` (`builtin`, `user`, `repository`).
2. **Per-instance random streams.** Each template instance contributes its
   instance id to the seed derivation of everything inside it (see
   [architecture, randomness](02-architecture.md) and ADR 0009).
3. **Updates and exchange:** see section 11.
4. **Curves:** predefined named curves first, described mathematically
   (see 03-scene-format.md section 11). The format leaves room for point-based
   curves later.

## 11. Template distribution and updates

Phonotope is offline-first with no accounts and no tracking, so there is no
central server that pushes updates. Principles:

- **Identity:** every template has a stable, namespaced id (for example
  `phonotope/sea` for built-ins, `user/<name>/<template>` for others) and a
  semantic version.
- **Built-in templates** update with the editor itself (a new editor release;
  as a web app that means the normal update of the app).
- **User templates** are files (a bundle with its own metadata, license, and
  assets) that can be exchanged by any means: file copy, e-mail, a Git repository.
- **Optional repositories:** a plain static folder or Git repository with an
  index file. The editor fetches from it **only on explicit user action**.
  Off by default. No automatic network access.
- **Update check is local:** the editor compares the recorded `origin` version
  of each group in a scene with the templates it currently has, and offers the
  update. The user decides.
- **Players never fetch anything.** They only read the scene bundle.
- **Assets inside templates** keep their own license records (same rules as scenes).

## 12. Still open

1. How conflicts between local overrides and a template update are shown in
   the UI without annoying the user.
2. Template dependencies: may a template depend on another template? (Simplest:
   no, a template is self-contained.)
3. How scene bundles get onto the phone (file picker import, share target,
   PWA file handling). To be checked during roadmap 0.3 and partly in the spike.
