# profiler-cli feedback (marAppInUseStageSuccessComplete_unix.js)

## Daemon dies silently loading a 60 MB gzipped per-test profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/bEBFvGOZTFODn5cpM1DoBA/runs/0/artifacts/public/test_info/profile_marAppInUseStageSuccessComplete_unix.js.json --session marAppInUseStageSuccessComplete_unix.js-1`
- Expected: the profile loads, or an error saying it is too large / out of memory.
- Got: `Error: Session ... is not reachable. Nothing is accepting connections on ....sock. The daemon exited without cleaning up.` The daemon log ends at `Fetching profile from ...` with no error line.
- Workaround: checked every failing job's per-test profile size with `curl -sI` (`x-goog-stored-content-length`) and loaded a 6 MB one instead. 26 of the 37 were over 29 MB (the xpcshell main thread's `DummyEvent` busy loop fills them with ~1M `Runnable` markers).

## Question: "at what time, to the millisecond, did this marker happen?" (long profiles)

- Command: `profiler-cli thread markers --session <resource-usage session> --search marAppInUseStageSuccessComplete_unix --list --limit 0`
- On a 24-minute resource-usage profile every row prints `t=12m3s`, so the updater launch and exit (1.07 s apart) print as `12m3s` and `12m4s`; `marker info` prints `Time: 12m3s (instant)` too.
- Needed `--json` and `start` to get 722.887 s / 723.953 s. Could have shown: milliseconds (`t=722.887s`, or `12m2.887s`) in list rows and `marker info`.

## Question: "was the machine saturated between t=A and t=B?"

- Command: `profiler-cli thread markers --session <resource-usage session> --search 'CPU Use' --list --limit 0` (after `zoom push 712,755`)
- Every row reads `CPU Use` with its duration; the `cpuPercent` field is not shown, and the profile has no counters (`counter list`: "No counters in this profile").
- Needed a script over `--json` to average `data.cpuPercent` per 2 s bucket. Could have shown: the CPU percent in each row's label, or a per-bucket summary like `counter info`'s "over time" section for `CPU Use` markers.

## Question: "which other tests were running while this test's updater ran?"

- Command: `profiler-cli thread markers --session <resource-usage session> --search unit_update_binary --list --limit 0 --json`, then a script selecting `test` markers overlapping [722.887, 723.953].
- The list gives each `test` marker's start (to the second) and duration, but not which overlap a given interval. Could have shown: an `--overlapping START,END` filter (or `zoom push` keeping only markers that overlap, flagged), plus end times in the list.
