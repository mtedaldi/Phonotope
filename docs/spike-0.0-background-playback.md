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
7. Do Chrome (Blink) and Firefox (Gecko) behave differently? Firefox is the
   maintainer's daily-driver browser; an Android WebView wrapper would use
   the Chromium engine regardless of the default browser.

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
| 8 | Firefox tab | direct | off | default | 60 min |
| 9 | Firefox tab | via audio element | on | default | 60 min |
| 10 | Firefox installed PWA (if offered) | best of 8-9 | | default | 60 min |

Rows 1-7 use Chrome. Firefox has no Battery API, so for rows 8-10 the battery
level is read manually at start and end.

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

## Findings so far (informal, Firefox on the test device)

- Run 1 (background, screen off, direct output, Media Session on): the page ran
  for 4:41 minutes and then stopped without any log entry; it did not restart.
  Up to then: no event droughts, maximum timer gap 0.3 s (no timer
  throttling), the audio context stayed `running`, a short phone call did
  not interrupt it.
- In both foreground and background the audio clock fell behind the wall clock
  at about 0.7 to 0.85 % (about 0.1 s per 10 to 15 s). Crackling and short
  gaps are audible on the phone. On the desktop the lag stays at 0.
- Without the convolution reverb the rate was practically unchanged
  (0.4 s per 60 s), so the reverb is not the main cause.
- Open: cause of the lag (clock/buffer handling versus load), cause of the
  stop after 4:41 minutes (process freeze or kill suspected; see
  `dumpsys activity exit-info` for the Firefox package), behavior in Chrome.

## Benchmark

[`spike-0.0/benchmark.html`](../spike-0.0/benchmark.html) runs a load ladder
(tone, noise, modulation, drops, reverb, N voices) plus probes for latency hint
and sample rate, and reports the lag rate per stage. Run it in the foreground in
each browser under test and paste the Markdown table below.

| Stage | Firefox | Chrome |
|---|---|---|
| L0 sine tone only | | |
| L1 noise | | |
| L2 modulation | | |
| L3 + drops | | |
| L4 + reverb | | |
| L5 + 16 voices | | |
| L6 + 48 voices | | |
| P1 to P4 probes | | |

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
| 8 | | | | | | |
| 9 | | | | | | |
| 10 | | | | | | |
