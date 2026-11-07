# 0003 The scene description is the source of truth

Status: accepted (2026-11-06)

## Context
The soundscape must not be tied inseparably to one editor or one engine.

## Decision
The scene description (JSON) is authoritative. The graph is a view and editing
tool for it. Players interpret the description.

## Consequences
Editor, player, and audio engine are interchangeable. The format needs a
specification, a version field, and a validator from the start.
