# fx-tests feedback (marAppInUseStageSuccessComplete_unix.js)

## Question: "are all these timeouts the same failure?"

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marAppInUseStageSuccessComplete_unix.js --task-ids --limit 0`
- Got: one issue, `37x TIMEOUT Test exceeded time limit`, and 37 task IDs.
- The 37 are three different failures, found only by scanning each job's `live_backing.log`: 35 wait for `callback.log` until killed, 1 has the updater exit `-1` (`[runUpdate : 2353] the process exit value should equal the expected value - -1 == 0`, then a hang at exit), 1 has `nsIProcess.run` throw `NS_ERROR_FAILURE`.
- Could have shown: for a timeout, the last assertion or `TEST-FAIL` before it, or the last log line of the test (the replayed log is already in the resource-usage profile), so that timeouts split into modes.

## `--bugs` with no bug prints nothing to say so

- Command: `fx-tests test <path> --bugs`
- Got: the same output as without `--bugs`, with no bugs section.
- Expected: a line such as `Bugs: none name this test`, so that "no bug" is not confused with "the flag was ignored".
