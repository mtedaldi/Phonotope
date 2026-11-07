# 0001 Player targets Android only

Status: accepted (2026-11-06)

## Context
No Apple hardware is available for testing. iOS also restricts background
audio more tightly.

## Decision
The first player targets Android only. Other platforms are possible later
because the scene format is platform-independent.

## Consequences
iOS background-audio constraints are not a design driver. Players for other
platforms can be added by implementing the scene format.
