## Question: the exact times of a few markers in a long profile (browser_dbg-worker-scopes.js)

- Command: `profiler-cli thread markers --session <s> --search ScriptExecution --list` on a 4m58s profile (cHn8tr-iRPK0ttrgoysI6Q).
- Expected: start times precise enough to order markers across threads within a few ms (I needed "did the main thread's `new Worker()` script return before the worker thread's first activity?").
- Got: `t=4m8s` for every marker in that second. Both markers on both threads read `4m8s`, so the list could not answer the question.
- Workaround: `profiler-cli marker info m-N --json | python3 -c '...start, end...'` for each marker, one call per handle.
- What would have answered it: millisecond precision in `--list` (e.g. `t=248.232s`) whatever the profile's length, or a `--precise` flag.
