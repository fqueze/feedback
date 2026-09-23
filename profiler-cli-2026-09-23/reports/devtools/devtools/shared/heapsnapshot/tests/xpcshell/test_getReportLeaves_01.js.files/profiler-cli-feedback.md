## In what order, to the ms, did these harness log lines happen?

- Command: `profiler-cli zoom push 924.5,927.8 --session test_getReportLeaves_01.js-1` then `profiler-cli thread markers --session test_getReportLeaves_01.js-1 --list --limit 0` on a 23-minute resource-usage profile (task IMKvf0T1THS6mvnmvpdqaA).
- Expected: timestamps precise enough to order the rows. About 280 markers fall in 3.3 s here, and the question is which of `chmod`, `launch_application`, `get_process_list` and the exception came first, a few ms apart.
- Got: `t=15m25s` on about 90 consecutive rows, and `t=15m26s` / `t=15m27s` on the rest. Same with `marker info` (`Time: 15m27s (instant)`).
- Workaround: `--json`, then read `start` for each marker in a script.
- What would have answered it: ms precision in `--list` when many rows share the printed second (e.g. `t=15m25.286s`), or a `--precise-times` flag.

## (review-test_getReportLeaves_01.js) Which marker is a link's `marker=N`?

- Command: checking 33 report links on one thread, each `…/marker-table/?marker=N&thread=0`, after `profiler-cli load <link> --session review-test_getReportLeaves_01.js-1`.
- Expected: a way to open marker index N directly, e.g. `profiler-cli marker info --index 57709`.
- Got: nothing takes an index. I found each candidate by its quoted time in a `--json` dump and checked `markerIndex` with `marker info … --json`.
- Workaround: a script mapping times to handles, then one `marker info` call over all the handles.
- What would have answered it: a lookup by `markerIndex`, or `markerIndex` printed in `--list` rows.

## (review-test_getReportLeaves_01.js) profile-link.py refuses a session loaded from a profiler link

- Command: `PROFILER_CLI_SESSION_OWNER=review-test_getReportLeaves_01.js python3 $D/profile-link.py --session review-test_getReportLeaves_01.js-1 --marker m-4065`, on a session loaded from the report's profiler.firefox.com link, as review-brief.md says to do.
- Expected: a link.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`.
- Workaround: loaded the raw Taskcluster URL into a second session and looked the markers up again.
- What would have answered it: profile-link.py taking the artifact URL out of a `from-url/` link.
