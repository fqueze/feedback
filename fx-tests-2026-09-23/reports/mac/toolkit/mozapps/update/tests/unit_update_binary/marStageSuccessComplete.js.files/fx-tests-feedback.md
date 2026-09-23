## Which failure mode is behind each of a test's timeouts

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marStageSuccessComplete.js` (and `--task-ids`, `fx-tests task <id>`).
- Expected: timeouts split by what the test was doing when killed.
- Got: all 32 are `TIMEOUT Test exceeded time limit` / `Test timed out`. Two different modes hide behind that: 31 wait on `Callback log does not exist yet`, 1 (IHWxoeYERfKJY1Bow_V4Fg) hangs in setup after `NS_ERROR_FILE_ACCESS_DENIED [nsIFile.permissions]`.
- Workaround: downloading the 32 `live_backing.log`s and grepping the replayed test logs (one of them came back gzip-encoded without `curl --compressed`).
- What would have answered it: for a TIMEOUT, the last distinct line (or last `TEST-INFO`/`JavaScript error`) of the replayed test log, grouped under Issues.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs: none" line.
- Got: the same output as without `--bugs`, so it is not clear whether the search ran.
