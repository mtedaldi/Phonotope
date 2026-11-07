# Phonotope Spike 0.0: background playback test page

A throwaway test page for one question: **does Web Audio keep generating
and playing sound reliably on Android with the screen off?**
It belongs to [spike 0.0](../docs/spike-0.0-background-playback.md). It is not
production code.

## What it plays

- looped white noise, low-passed, with two slow LFOs (modulation load)
- randomly timed "drops" (short sine blips) with random pan and low-pass
  ("distance"), routed through an optional convolution reverb (CPU load)
- an event scheduler that plans events ahead on the **audio clock**
  (configurable look-ahead and tick), as planned for the real engine

All randomness comes from seeded streams derived from the seed text.
The PRNG here is a spike convenience, not the final choice (ADR 0009).

## Running it

Installable PWAs and some APIs need HTTPS, so the easiest way is GitHub Pages:

1. In the repository: Settings, Pages, deploy from branch `main`, folder `/ (root)`.
2. Open `https://mtedaldi.github.io/Phonotope/spike-0.0/` on the phone (Chrome).
3. For the PWA variant: Chrome menu, "Install app" / "Add to Home screen"
   (labels vary), then start it from the home screen icon.

## Test procedure

1. Set a **low volume**, unplug the charger, close other audio apps.
2. Choose settings (see the matrix in the spike document), press **Start**.
3. Switch the screen off with the power button. Do not use a wake lock.
4. Wait the planned time (60 minutes; once overnight for the best variant).
5. Wake the phone and read **Status** and **Log**. Press **Copy** or
   **Download** and paste the result into the spike document.
6. If the page seems to have been killed, reload it: **Previous run** shows the
   last heartbeat and whether the run ended cleanly.

## Reading the numbers

| Metric | Meaning |
|---|---|
| Audio lag vs wall clock | Wall time minus audio-clock time. Near 0 means audio ran continuously. Growing lag means the audio clock was suspended or stalled. |
| Audio clock stalls | Moments when the audio clock fell behind by more than 0.5 s |
| Event droughts | The look-ahead ran dry before the scheduler woke up again. Raise the look-ahead until droughts disappear. |
| Scheduler ticks, max gap | How much the browser throttled the timer while hidden |
| Time hidden | Total time the page was in the background |
| Battery | Level at start and now (if the Battery API exists). Needs an unplugged phone. |

The log also records every `AudioContext` state change and visibility change.

## Variants worth testing

- Output path: direct to destination versus via an `<audio>` element (the latter
  is the usual way to get notification controls and media-playback priority).
- Media Session on or off.
- Chrome tab versus installed PWA.
- Look-ahead 2, 5, 10, 30 s.
- Battery optimization for Chrome: default versus relaxed
  (the Android battery settings for Chrome; labels depend on the manufacturer).
  Some Android vendors manage background apps aggressively, so record the
  setting used with each result.

## AI-assisted development

This page was written with the help of Claude (Anthropic) from a specification
by the maintainer. The page logic was checked for syntax and exercised with a
mocked Web Audio environment, but not yet on a real device. Treat the first real
test as part of the experiment.

## License

MIT, as the rest of the repository.
