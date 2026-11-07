# 0009 Per-node deterministic random streams

Status: accepted in principle (2026-11-06); algorithm choice open

## Context
Randomness is central (events, noise, variation). We want reproducibility for
debugging and sharing, and we need identical template instances not to produce
identical (correlated) sound.

## Decision
- Every random consumer owns an independent stream.
- Stream seed = hash(scene seed, node path), where the path contains the ids of
  enclosing template instances.
- The scene format specifies the PRNG algorithm and hash (a small, well-known,
  documented generator) so all players agree. The exact choice is made before
  format 0.1 is frozen.
- Event times live in scene time (audio clock).
- Proposed default: fresh scene seed at each player start; pinned seeds for
  debugging and sharing.

## Consequences
- Reproducible across players; no coupling between nodes.
- Identical noise in two nodes is avoided by construction.
- Exact reproducibility is not promised under live parameter changes.
