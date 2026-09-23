## Default session directory not writable in the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_net_simple-request-data.js profiler-cli load <taskcluster url> --session browser_net_simple-request-data.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session ...` from the next command.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the error message suggested it). The subagent brief does not mention it, so every agent pays one failed load.

## Question: "at what time, to the millisecond, did each of these test assertions happen?"

- Command: `profiler-cli thread markers --category Test --search browser_net_simple-request-data --list --limit 0`
- Expected: times precise enough to order and space the assertions (they are 20-200 ms apart).
- Got: every row past the first minute printed as `t=3m25s`; 250 markers spanning 0.5 s all share one timestamp, and the waitFor failure 30 s later is `t=3m55s`. Needed `--json` and a script over `flatMarkers[].start` to get 204.992 s, 205.133 s, 234.687 s.
- What would have answered it: sub-second precision in `--list` time column (e.g. `t=3m25.133s` or seconds with 3 decimals, which is also what `profile-link.py --range` takes).

## Retry profile opened on a content process thread; Test-category search silently empty

- Command: `profiler-cli load .../profile_browser_net_simple-request-data-2.js.json --session ...-2` then `profiler-cli thread markers --category Test --search 'TEST-UNEXPECTED,...' --list --limit 0 --json`
- Expected: the parent process GeckoMain selected, as with the first profile of the same job.
- Got: `t-17 (GeckoMain, Privileged Content)` selected; the search returned 0 markers with no hint that the Test markers live on another thread. Easy to misread as "no failures in the retry".
- Workaround: `profiler-cli thread select t-0`. An empty Test-category result could say which thread holds that category's markers.
