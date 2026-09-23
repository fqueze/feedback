## Times past 60 s print as `1m22s`, which cannot order events within a second

- Command: `profiler-cli marker info m-68 m-70 m-58 m-63 m-57 m-69 --session ...-2` on a 2m50s profile (task CtH80BhATXWWiaPNXI7nNA)
- Expected: millisecond times, as for profiles under 60 s (`t=42.635s`).
- Got: `Time: 1m22s - 1m22s` for all six markers, which happen within 45 ms of each other and whose order is the finding.
- Workaround: `thread markers --list --json` and read `start`.

## `load` selected the WebExtensions main thread, not the parent's

- Command: `profiler-cli load .../profile_browser_dbg-scroll-run-to-completion-2.js.json --session ...-3`, then `thread markers --category Test ...`
- Expected: the parent process GeckoMain selected, as with the first profile of the same job.
- Got: `t-15 (GeckoMain, WebExtensions)` selected, so the test-log query returned "0 markers" with no hint that it read the wrong thread.
- Workaround: `thread select t-0` first.
