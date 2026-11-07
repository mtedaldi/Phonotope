# 0002 Web Audio is the working assumption, pending spike 0.0

Status: proposed (2026-11-06)

## Context
Godot was the initial candidate. Web Audio provides filters, delay, panner,
convolution reverb, and worklets natively (to be verified), runs in a browser
for the editor, and could run as PWA/WebView on Android. One engine for editor
and player avoids sound differences between preview and playback.

## Decision
Assume Web Audio for editor and player. Confirm or reject it with
[spike 0.0](../spike-0.0-background-playback.md).

## Consequences
If the spike fails, the player becomes native and the format is the contract
between editor and player. Godot remains a fallback candidate.
