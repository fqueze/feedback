## `thread markers --search 'initializeFOG,FOG,fog' --top-names 20` times out on a 3M-marker parent main thread

- Command: `profiler-cli thread markers --session browser_usercharacteristics_gamepads.js-1 --thread t-0 --search 'initializeFOG,FOG,fog' --top-names 20` (per-test profile of task B4bdVfUSSSy9A0fdw7zziA, 3,089,567 markers on t-0)
- Expected: an aggregate of markers mentioning FOG.
- Got: `Error: Timed out after 30000ms waiting for the daemon ... to answer.` The next command worked, so the daemon survived.
- Workaround: `--search 'name:fog'` came back in a few seconds. It would help if the timeout error suggested narrowing to a field (`name:`), or if a bare-term search over all payload fields streamed partial results instead of timing out.

## (review) Question: "which tests ran in this browser session, with their status" — `name:test` is a substring match

- Command: `profiler-cli thread markers --session review-browser_usercharacteristics_gamepads.js-2 --category Test --search "name:test" --list --limit 0` (per-test profile of task B4bdVfUSSSy9A0fdw7zziA)
- Expected: only the per-test `test` markers (one row per test file, with PASS/FAIL).
- Got: 1,894 rows, mostly `TEST-PASS` markers, because `name:test` matches any name that contains "test", ignoring case.
- Workaround: piped the text output through `awk '$2=="test"'`. An exact-match form such as `name:=test`, or a `--name` flag, would answer this directly.
