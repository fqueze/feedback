## Times past 60 s lose all precision in text output

- Command: `profiler-cli thread markers --category Test --search browser_layoutHelpers_getBoxQuads2 --list --limit 0 --session …` and `profiler-cli screenshots --range 120.0,120.3 -o dir --session …`, on a 2-minute per-test profile (task HFxbylKeTpW144yJJMGZKw).
- Expected: `t=120.225s`-style times, as for markers under 60 s (`t=2.610s`).
- Got: every marker and screenshot printed as `t=2m` (or `t=2m1s`), so the order of events 5 ms apart, which is the whole question in a race, cannot be read.
- Workaround: `--json` and a Python one-liner printing `start/1000` for each `flatMarkers` / `screenshots` entry, on every listing.
- The question the default output did not answer: "in what order, to the millisecond, did these markers happen, late in a long profile".

## `screenshots` prints frame times ~7.5 ms later than the same markers everywhere else

(from review-browser_layoutHelpers_getBoxQuads2.js)

- Command: `profiler-cli screenshots --range 2.54,2.62 -o dir --json --session …` on task Xi9__pyfSKyCLqNSvZu5_Q's per-test profile, next to `thread markers --search CompositorScreenshot --list --json` on the GPU process main thread (t-14) of the same session. Same on task HFxbylKeTpW144yJJMGZKw (t-133).
- Expected: one time per frame, the marker's start, which lines up with the Renderer's `Composite #N` for that window (e.g. `Composite #2` 2.5943s, screenshot 2.5945s).
- Got: `screenshots` puts every frame a constant ~7.4 ms (Windows) / ~7.8 ms (macOS) later than `thread markers`, `marker info` and `marker screenshot` do for the same handle: m-11 2.6019s vs 2.5945s; on macOS m-228 120.2054s vs 120.1976s. The shift reorders frames against other events: the report read the fission window's last screenshot as 1 ms before the compositor pause, after the window's later composites (`Composite #6` at 120.1982s), when it came before them.
- Workaround: take frame times from `thread markers --search CompositorScreenshot --json` and only the images from `screenshots`.
- The question the default output did not answer: "which frame was on screen when this event happened".
