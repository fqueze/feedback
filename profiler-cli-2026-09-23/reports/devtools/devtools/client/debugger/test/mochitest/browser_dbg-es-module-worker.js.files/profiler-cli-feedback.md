## Which of these markers, all in the same second, came first?

- Command: `profiler-cli thread markers --thread t-189 --list --limit 0 --search "DevToolsProcess,worker.sys.mjs,ScriptExecution" --session browser_dbg-es-module-worker.js-2` on a 4m19s per-test profile (task Tp_HLaWoTUmUXTF0GQz1ng).
- Expected: start times with sub-millisecond precision, as the same command prints on a 47 s profile (`t=2.936s`).
- Got: every row `t=3m30s`. The question was whether the content main thread returned from `new Worker()` before the worker thread finished initialising, a sub-millisecond ordering across two threads, and the list could not order anything within that second. `marker info` is no better in text.
- Workaround: `--json` on `thread markers --list` and `marker info`, then jq on `.start`. A `t=209.5848s` (or `3m29.5848s`) format past the one-minute mark would have answered it directly.

## Which content-process worker threads have this marker, and which do not?

- Command: `profiler-cli profile markers --search Msg_RegisterDone --session browser_dbg-es-module-worker.js-2` (199 threads, ~130 worker threads).
- Expected: a way to see the matching threads of one process type, or the worker threads that lack the marker.
- Got: the "Matches by thread" table stops after 10 rows (`... and 122 more threads; --json lists them all`), all parent-process workers, and the rows list thread handles without process names, so the content-process workers (the ones that mattered) were not in view. `--limit 0` did not reach the table.
- Workaround: `--json` for `.byThread[]`, plus `thread list --json` for the process of each DOM Worker, joined with jq. A `--process`/`--search`-on-process filter on `profile markers`, or `--limit 0` also expanding the by-thread table, would have answered it.
