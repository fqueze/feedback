## `--limit 0` does not expand a try row's task list

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- Expected: every task of the `browser_application_panel_target-switching.js` row.
- Got: 5 task lines (each task listed twice, once per failing execution) then `… 1 more task`, with `--limit 0` given.
- Workaround: `--json` and reading `newIntermittents[].taskIds`.

## Question: did the test run on the config of the other removed `skip-if` (debug http3)?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --task-ids --json`
- The question: which configs of this push ran this test and passed, and whether a job matching each removed `skip-if` condition ran at all.
- Got: only the failing entry (`totalRuns: 7`, `jobNames` = the failing config); nothing on passing configs or on configs the push did not schedule. `http3` job names appear in the JSON, but from central history, which misled me into thinking the push had http3 jobs.
- Workaround: listed the push's jobs from the Treeherder API (`/api/jobs/?push_id=...`) and found no http3 job.
- What could have shown it: a per-test "ran on" line in `try --all-jobs` (config, runs, passes), and a "configs matching the removed skip-if that this push did not run" note.
