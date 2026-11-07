# 0010 Templates: built-in first, user templates later, no mandatory network

Status: accepted (2026-11-06)

## Decision
- The editor ships built-in templates first. User-created templates come later.
- Both use the same template format, so no redesign is needed.
- Templates have namespaced ids and semantic versions.
- Distribution of user templates is by files or optional repositories fetched
  only on explicit user action. No automatic network access; players never fetch.
- Update checks compare locally against the templates the editor holds.

## Consequences
Fits the offline-first, no-tracking principles. Built-in updates ride on editor
releases.
