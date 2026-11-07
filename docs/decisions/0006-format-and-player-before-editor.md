# 0006 Scene format and player come before the editor

Status: accepted (2026-11-06)

## Context
A node editor is the largest single piece of work. The initial requirements
bundled editor, format, mobile player, and background playback into one version.

## Decision
Define the scene format first and build the player against hand-written scenes.
The editor follows once the format has proven itself.

## Consequences
Early versions need no editor UI. The format is stress-tested by real use
before an editor locks it in.
