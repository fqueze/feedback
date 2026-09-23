## Default session directory not writable in the sandbox
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_usercharacteristics_css.js-1`
- Expected: session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session`. The hint to set PROFILER_CLI_SESSION_DIR was good.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (and for profile-link.py).

## Multi-term marker search over a 3.1M-marker thread times out
- Command: `profiler-cli thread markers --search 'idle-startup,initializeFOG,StartupTelemetry,perWindowIdleTask,browser-idle-startup,FOG' --list --limit 60`
- Expected: a list (or an empty one).
- Got: `Timed out after 30000ms waiting for the daemon ... to answer.` The parent main thread had 3,114,084 markers, 2.28M of them `Preference Read`.
- Workaround: one term at a time, or `--search NotifyObservers --group-by field:name` and grep.

## Selected thread after load is not the parent main thread
- Command: load of `aQj9S2KXSJm5kttyRZgjIw/.../profile_browser_usercharacteristics_css.js.json`, then `thread markers --search fog.testResetFOG --list`
- Expected: parent-process GeckoMain selected, as in the other profile.
- Got: `t-15 (GeckoMain, Privileged Content)` selected; "0 markers" with no hint that another thread has them.
- Workaround: `thread select t-0`.

## Question: in which order did markers within the same second happen?
- Command: `thread markers --search 'css_system,...' --list` prints `t=1m50s` for the metric set, the TEST-UNEXPECTED-FAIL and the Ping::submit alike.
- Needed: millisecond order (set at 109.5987 s, done notification 110.1257, fail 110.1273, submit 110.1285).
- Workaround: `marker info m-a m-b ... --json` and a python one-liner over `.markers[].start`. Printing ms precision once times are past 1 minute would have answered it.

## Question: list the per-test `test` markers (the job's test sequence) of a per-test profile
- Command: `thread markers --category Test --search 'name:test,name:fog.testResetFOG' --list --limit 0`
- Got: `name:test` is a substring match, so it returned 1,895 markers (every TEST-PASS etc.).
- Workaround: `--list --limit 0` piped through `rg '^\s+m-\d+\s+(test|task)\s'`. An exact-name filter would have answered it.
