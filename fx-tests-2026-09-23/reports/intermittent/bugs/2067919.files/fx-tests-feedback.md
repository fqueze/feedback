# fx-tests feedback (bug 2067919 report)

## `intermittent --bug` headline is harness noise; crash-only occurrences are invisible

- Command: `fx-tests intermittent --bug 2067919 --since 21`
- Expected: the bug's own failure (an OOM PROCESS-CRASH) somewhere in "Failure messages", and a count of occurrences carrying no line.
- Got: `543x finished in <n>ms` on top (the harness's per-test duration line, emitted for every failing test), then leaks. The 75 win32 OOM jobs that are the bug's summary carry no line at all (PROCESS-CRASH is not a TEST-UNEXPECTED-FAIL line), and nothing says 75 of 626 occurrences had none. The "(none: ...)" note only shows when *all* have none.
- Workaround: `--json` and a script over `occurrenceRows[].lines` to split rows with/without lines per platform.
- Suggest: drop `finished in <n>ms` / `profile uploaded in ...` from the message ranking; print "N occurrences carried no TEST-UNEXPECTED-FAIL line (crash-only?)" with their platforms.

## Question: "how did the process's memory evolve, test by test, up to this OOM crash?"

- Command: `fx-tests task <taskId>` / `--profiles` on OOM jobs (e.g. `Pc5L6cPkSuylXSYtXRXH5g`).
- The answer is in the log: browser-chrome prints `MEMORY STAT | vsize .. | vsizeMaxContiguous .. | residentFast .. | heapAllocated ..` after every test. `fx-tests task` does not surface it, and win32 OOM jobs have no per-test profile.
- Workaround: download `live_backing.log`, script over TEST-START / MEMORY STAT / PROCESS-CRASH for the manifest.
- Could show: for a crash whose reason is OOM, the per-test vsize / vsizeMaxContiguous series of the crashed process, and the test with the largest drop.

## Crash "(finished)" counted in `task` but not in `test` / `crashes`

- `fx-tests task PRIon_aeR72MsHGuLcpkCw` lists `browser_aiwindow_group_tabs_button.js` as `CRASH @ CCGraphBuilder::AddNode` (the PROCESS-CRASH line names it "(finished)").
- `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button.js --history --config windows11-32-25h2/debug` says 277 pass, 0 crash; `fx-tests crashes --path browser/components/aiwindow --signature CCGraphBuilder` does not count it either.
- Expected: same attribution in both, or the aggregate saying "(finished)" crashes are excluded. As is, a test that kills the browser just after finishing looks clean in `test`.

## Unsuffixed per-test profile can be the rerun's

- `fx-tests task Bk_R67v0Q1eJzz8VkcQzjw --profiles` shows one profile, `profile_browser_aiwindow_monitor_button.js.json`, under a test with "2 failing executions of 2".
- The first execution's capture failed (`failed to upload profile: ... NS_ERROR_OUT_OF_MEMORY` / `Component is not available` in the log), so the unsuffixed name went to the rerun: loading it showed the rerun session (starts with `browser_aiwindow_firstrun.js`, 46 s long), not the session that crashed.
- Suggest: when the log has `failed to upload profile` for the first execution, say which execution the listed profile belongs to.

## Question: "a passing run of manifest M on config C near date D"

- Needed to compare a crashing pre-fix win32 opt run with a passing one. No command gives the tasks of a config that ran a given manifest; I went through the Treeherder push API and grepped ~25 logs for `Running manifest:`, and found none passing on the 3 pushes tried.

## (review-2067919) Question: "the jobs of config C that ran test T, passing ones included, to read their logs"

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_group_tabs_button.js --config windows11-32-25h2/debug --task-ids --limit 0`
- Expected: the task IDs of the 277 runs it counts, so I could read what the process did later in the manifest.
- Got: `277 pass`, then an empty `Task IDs` list: `--task-ids` only lists failing runs.
- Workaround: the Treeherder push API over 40 mozilla-central pushes, keeping the jobs named `test-windows11-32-25h2/debug-mochitest-browser-chrome-25`. Six of them died of a CC OOM later in the same manifest, which `test` could not show for this test.
- Could show: `--task-ids --all` (or `--pass`) listing the task IDs of passing runs as well, with their date.
