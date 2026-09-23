## The failure line of each annotated job (perf jobs)

- Command: `fx-tests intermittent --bug 1682027 --limit 0`
- Expected: the failure message of each of the 32 annotated jobs, so that misstarred jobs stand out.
- Got: "Failure messages, per annotated job" lists only 2 (the two with a `TEST-UNEXPECTED-FAIL` line). The 30 raptor `results.MissingResultsError: ...` lines, which are the bug's actual failure, are not shown, and neither is the talos job's `Automation Error: mozharness timed out`.
- Workaround: downloaded all 32 `live_backing.log` files and grepped `MissingResultsError|TEST-UNEXPECTED-FAIL|Automation Error`. That found 28 FCP + 1 LCP (bing), 1 FCP (google-maps), and 2 misstarred jobs.

## How often a perf (browsertime/raptor) job fails, and since when

- Question: the pass/fail rate of `browsertime-tp6m-fenix-bing-search-restaurants[-nofis]` per day/push, and whether it changed.
- Command: none available. `fx-tests test` takes a test path, and `fx-tests task <id>` on a browsertime job says "This profile records no tests at all".
- Workaround: Treeherder REST `api/project/<repo>/jobs/?job_type_name=...&last_modified__gt=...` plus `push/?id__in=...`, then a script. That showed a step from ~4% to ~27% of jobs around 2026-09-16/17.
- What would have answered it: `fx-tests test`/`--history` accepting a job name (or a raptor test name) for perf jobs.
