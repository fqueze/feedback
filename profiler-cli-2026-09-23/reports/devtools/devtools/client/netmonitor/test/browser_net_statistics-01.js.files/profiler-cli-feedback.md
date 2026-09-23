## Marker times rounded to whole seconds in long profiles

- Command: `profiler-cli marker info m-1 m-169 --session browser_net_statistics-01.js-2` (a 31m50s resource-usage profile)
- Question: how long after "Wait for completion of all NetworkUpdateEvents packets..." did the test time out, and how far apart were the helper's log lines.
- Expected: millisecond start times, as `thread markers --list` gives on short per-test profiles.
- Got: `Time: 25m8s (instant)` for every marker in that second, in both `thread markers --list` and `marker info`; a dozen log lines all read `t=25m8s`.
- Workaround: `marker info ... --json` and read `start`.

## `field:value` search on a test path does not narrow to the `test` marker (review)

- Command: `profiler-cli thread markers --session review-browser_net_statistics-01.js-2 --search 'test:devtools/client/netmonitor/test/browser_net_statistics-01.js' --list` (MdqcyghX resource-usage profile)
- Question: the `test` marker of this one test, to check its `markerIndex`.
- Expected: the single `test` marker whose `test` field is that path (plus perhaps its `FAIL`/`PASS` instants).
- Got: 44 markers, led by `DocShell` and `DOMWindow` about:blank markers that have no `test` field; the `test` marker was not in the first screen.
- Workaround: bare `--search statistics-01 --list`, then grep for the ` test ` name column.
