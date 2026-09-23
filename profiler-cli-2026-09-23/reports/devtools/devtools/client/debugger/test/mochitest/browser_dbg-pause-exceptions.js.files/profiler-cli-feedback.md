## Stale view range for `--thread` after `zoom clear`

- Command: after `zoom push m-1435` (a 5 s window), `thread samples --thread t-1 ...`, then `zoom clear`, then
  `profiler-cli thread functions --thread t-1 --search EGL --session browser_dbg-pause-exceptions.js-1`
  (and `thread samples --thread t-1`), selected thread t-0.
- Expected: the Renderer's samples over the full 6m16s profile (28,920 samples, as `zoom push 0,376.3` then
  the same command gives).
- Got: header says `View: Full profile`, but only 580 running samples and 10 functions: seemingly the
  samples of the previously zoomed 5 s window (or similar), so a search for `EGL` returned nothing although
  `libEGL_mesa.so.0!dri2_swap_buffers_with_damage` is in the thread.
- Workaround: `zoom push 0,<end>` explicitly instead of relying on `zoom clear`.
- Cost: I nearly concluded the renderer never called EGL/mesa functions.

## Question: "how busy was the machine over this range" (resource-usage profile)

- Command: `profiler-cli zoom push 283,420 --session browser_dbg-pause-exceptions.js-ru` then
  `thread markers --search "name:CPU Use" --list --limit 0 --json`, averaged `cpuPercent` with a python script.
- Wanted: the mean / max machine CPU% over a zoomed range. `profile info` reports "No significant activity" and
  `counter list` "No counters" on the resource-usage profile, so its CPU track is only reachable as ~1,300 `CPU Use`
  markers per 2 minutes.
- Could have shown: a CPU summary (mean, p90, max) in `profile info` or `thread markers` aggregate for CPU markers
  within the zoom.
