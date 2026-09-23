## Question: "how big is this per-test profile, before I load it"

- Command: `fx-tests task FJATl84VR8iclfuypyQVbA --profiles`
- Expected: some hint that `profile_marPostUpdateEnvironment.js.json` is 62 MB gzipped / 875 MB decoded, too large for `profiler-cli` to load.
- Got: just the URL. `profiler-cli load` then died silently on it (see profiler-cli-feedback.md). The job's resource-usage profile already records it: an `artifact` marker "profile_marPostUpdateEnvironment.js.json — 471MB" in OH-SrnKTQR2OByrOQSgJpw.
- Workaround: `curl -sI` each candidate and read `x-goog-stored-content-length` across all 57 failing tasks to find the smallest. What would have answered it: the size next to each profile URL in `task --profiles`, and a warning above ~500 MB decoded.

## Question: "in which failing runs does the replayed log contain <line>" (here `launchchild_osx: Failed to run application`)

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marPostUpdateEnvironment.js --task-ids --limit 0`
- Expected: a way to split the 57 `Test timed out` failures by a line in their log. The first failure message is the harness's "Test timed out" in every case, so `--issue` cannot tell apart the 53 callback waits (3 of them with an updater errAETimeout NSLog) from the 4 `NS_ERROR_FILE_ACCESS_DENIED` setup errors.
- Got: one issue, "57x TIMEOUT Test exceeded time limit".
- Workaround: downloaded the 57 resource-usage profiles and grepped the raw JSON (`marPostUpdateEnvironment.js.files/nslog-scan.txt`, `updater-durations.txt`). What would have answered it: a `--grep <text>` over each failing run's replayed log, or listing the last non-harness error/JavaScript error before a timeout as a sub-issue.
