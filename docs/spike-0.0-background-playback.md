# Spike 0.0: Android background playback

Status: planned. Results to be entered below.

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

## Test signal

A deliberately small test page: continuous generated noise plus randomly timed
short "drops" on a seeded schedule, with a log of intended versus actual
trigger times. Keep it minimal; it is throwaway.

## Test matrix

| Variant | Screen | Battery saver | Duration | Notes |
|---|---|---|---|---|
| Chrome tab | off | off | 60 min | baseline |
| Installed PWA | off | off | 60 min | |
| Installed PWA | off | on | 60 min | |
| WebView wrapper + foreground service | off | on | 60 min | only if PWA fails |

Run at least once overnight (8 h) for the best variant.

## Measure

- Audible dropouts (count, by listening and by log)
- Timing error of events: intended versus actual (median, worst case)
- Battery percentage per hour
- Behavior on interruptions (question 6)
- Phone model, Android version, Chrome/WebView version

## Decision rules

- **Pass** (no dropouts, timing error well below event spacing): Web Audio is
  the player engine; proceed with 0.1.
- **Partial** (works only with wrapper/foreground service or longer look-ahead):
  adopt that variant, record the required look-ahead in the architecture.
- **Fail**: native player (e.g. Oboe or miniaudio) that reads the same scene
  format; the browser stays the editor. Record the sound-parity risk.

## Results

| Variant | Dropouts | Timing error | Battery/h | Verdict |
|---|---|---|---|---|
| | | | | |
