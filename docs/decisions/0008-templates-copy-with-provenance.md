# 0008 Templates are copied into the scene, with provenance

Status: accepted (2026-11-06)

## Context
Templates (prebuilt groups with exposed parameters and macros) can be
referenced live from a library or copied into each scene.

- Live reference: updates are easy, but old scenes can break and every player
  needs the template library.
- Copy: scenes are self-contained, but template improvements do not arrive
  automatically.

## Decision
Inserting a template copies its graph into the scene and records `template id`
and `version` on the group. The editor can offer an explicit update, which the
user confirms.

## Consequences
- Scenes stay self-contained and portable; players need no template knowledge.
- The user controls when and whether a template update is applied.
- The editor must track local overrides so updates can warn about conflicts.
- The scene format needs group interfaces, macros, a dashboard section, and an
  origin record (see 03-scene-format.md section 9).
