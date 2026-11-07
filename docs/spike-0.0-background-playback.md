# Spike 0.0: Android background playback

Status: test page ready (`spike-0.0/`), results to be entered below.

## Purpose

The biggest unknown for a web-based player is whether an Android phone keeps
generating and playing sound reliably with the screen off. This decides whether
Web Audio can be the engine for both editor and player (ADR 0002).

## Questions

1. Does playback continue for 60+ minutes with the screen off and the phone locked?
2. Are randomly scheduled events still on time, or are timers throttled?
3. How much look-ahead scheduling is needed to stay gapless?
4. Does it behave differently as an installed PWA versus a WebView wrapper
   (with a foreground service)?
5. What is the battery use per hour?
6. What happens on an incoming call, another app starting audio, headphone
   removal, or Bluetooth disconnect?

## Test device

OnePlus Nord 5, build `CPH2719_16.0.5.1300`. Chrome version to be recorded
from the page's Environment block.

## Test page

[`spike-0.0/`](../spike-0.0/) (see its README for hosting and procedure):
generated noise with slow modulation, randomly timed drops through a
convolution reverb, seeded randomness, an event scheduler with configurable
look-ahead, optional playback through an `<audio>` element, optional Media
Session, and a log that records audio-clock lag, event droughts, timer gaps,
state changes, visibility, and battery level.

## Test matrix

| # | Variant | Output path | Media Session | Battery optimization (Chrome) | Duration |
|---|---|---|---|---|---|
| 1 | Chrome tab | direct | off | default | 60 min |
| 2 | Chrome tab | via audio element | on | default | 60 min |
| 3 | Installed PWA | direct | on | default | 60 min |
| 4 | Installed PWA | via audio element | on | default | 60 min |
| 5 | best of 1-4 | | | relaxed | 60 min |
| 6 | best overall, look-ahead tuned | | | best | 8 h overnight |
| 7 | WebView wrapper + foreground service | | | | only if 1-5 fail |

Screen off in all runs, charger unplugged, same volume. Vendor battery
management may differ from stock Android, so each result records the setting used.
Interruption tests (question 6) are done once on the best variant.

## Measure

- Audio lag versus wall clock, audio-clock stalls, event droughts
- Scheduler tick gaps (timer throttling) and time hidden
- Battery percentage per hour
- Audible dropouts by listening, compared with the log
- Behavior on interruptions
- Chrome version and Android build

## Decision rules

- **Pass** (no dropouts, no event droughts at a modest look-ahead, audio lag stays
  near zero): Web Audio is the player engine; proceed with 0.1.
- **Partial** (works only with wrapper/foreground service or a long look-ahead):
  adopt that variant, record the required look-ahead in the architecture.
- **Fail**: native player (e.g. Oboe or miniaudio) that reads the same scene
  format; the browser stays the editor. Record the sound-parity risk.

## Results

| # | Dropouts | Max audio lag | Droughts (max) | Max tick gap | Battery/h | Verdict |
|---|---|---|---|---|---|---|
| 1 | | | | | | |
| 2 | | | | | | |
| 3 | | | | | | |
| 4 | | | | | | |
| 5 | | | | | | |
| 6 | | | | | | |
