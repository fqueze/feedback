## `thread samples --include-idle` shows 3 samples for a 99 ms zoom at a 10 ms interval

- Command: `profiler-cli zoom push 13.707,13.806 --session <s>` then `profiler-cli thread samples --include-idle --session <s>` (profile `XzvJ4VKtSuGqLmkt8QN8fA` / `profile_browser_dbg-javascript-tracer-sidebar-toggle.js.json`, parent GeckoMain).
- Expected: about 10 samples, idle ones included, so I could tell whether the main thread was busy or idle during the window.
- Got: `Categories (3 running samples)`, no idle category, no word on where the other ~7 samples went (not recorded while asleep? dropped?). I could not tell busy from idle from this output.
- Workaround: read `Runnable` markers with `--min-duration` in the zoom to see what the thread ran.
