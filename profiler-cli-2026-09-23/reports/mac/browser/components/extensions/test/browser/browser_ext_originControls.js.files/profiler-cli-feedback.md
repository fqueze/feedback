## Default session dir not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session browser_ext_originControls.js-1`
- Expected: a session loaded.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, followed by a second confusing error `Unknown session ...: no metadata found` from the next command in the same shell line.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`. The brief does not mention this variable; `profile-link.py` does honour it.

## Zoomed marker list is cluttered by markers that merely overlap the window

- Question: "what happened on the main thread between t=4.347 and t=4.360?"
- Command: `profiler-cli thread markers --list --limit 0` after `zoom push 4.347,4.360`
- Got: the first ~20 rows are multi-second markers that started long before (CSS animation, AsyncShutdown blocker, TTFI, task, Awake), which I had to exclude one by one with `-name:` terms.
- Could have shown: an option to list only markers that start inside the zoom range (or to sort the overlapping long ones last).

## Empty samples output without saying why

- Command: `profiler-cli thread samples-top-down` after `zoom push 4.3440,4.3470`
- Got: `Top-Down Call Tree:` followed by nothing.
- Expected: "0 samples in this range (sampling interval N ms)" so it is clear the range is just smaller than the sampling interval.
