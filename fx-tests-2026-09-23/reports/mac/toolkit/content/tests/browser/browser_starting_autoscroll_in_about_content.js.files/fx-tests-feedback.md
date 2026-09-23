## Which jobs had the harness retry pass

- Question: "in which failing jobs did the retry pass", to get a profile of a passing run of the test.
- Command: `fx-tests test <path> --task-ids --limit 0`, then `rg -o '<taskid>' | sort | uniq -c` to find task IDs listed once rather than twice.
- Expected: a per-job marker in the task list (e.g. `retry passed` / `retry failed`), since the header already says "280 of 373 failing jobs saw the failure more than once".
- Got: each job repeated once per failing execution, with no label; the passing-retry jobs are only found by counting duplicates. `fx-tests task <id>` then does say "Passed when the harness reran it", so the data is there.

## One shutdown's two messages shown as two failure modes

- Command: `fx-tests test <path>`
- Got: `Issues (first failure per run)`: `382x leaked window until shutdown [url = about:support]` and `271x leaked 1 window(s) until shutdown [url = about:support]`.
- Every failing shutdown prints both (the first from browser-test.js, the second from runtests.py's leak parser), so these are one failure mode, not two. The split reads as two modes to diagnose; I had to check in the profile that both come from the same shutdown.
