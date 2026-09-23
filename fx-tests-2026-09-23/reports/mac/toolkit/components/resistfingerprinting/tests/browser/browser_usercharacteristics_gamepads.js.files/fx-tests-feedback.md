## Question: "a job on the same push/config where this test passed", to compare with a failing one

- Needed: the resource-usage profile of a passing run of `browser_usercharacteristics_gamepads.js`, ideally on the failing push (autoland c68d29eb2646), to check whether a log line seen in every failing job ("[ERROR glean_core] Glean should not be initialized multiple times" during the manifest's first test) is also in passing ones.
- Commands tried: `fx-tests test <path> --task-ids` (failing tasks only), `fx-tests test --help` (no option lists passing tasks).
- Workaround: a Python script over the Treeherder jobs API (`/api/jobs/?push_id=2040839`, paginated) to list the push's `macosx1500-aarch64/opt-mochitest-browser-chrome-*` tasks, then `fx-tests task <id> --passed --limit 0 | grep` on each chunk to find the one that ran the manifest (chunk 6, QUMXw-6dTw-de0aox_M7Lw). That took 8 `fx-tests task` calls.
- What would have answered it: `fx-tests test <path> --task-ids --passed` (or `--coverage --task-ids`) printing a few passing task IDs per config, preferably on the same revisions as the failures.

## `fx-tests test <path> --bugs` printed no Bugs section at all

- Command: `fx-tests test toolkit/components/resistfingerprinting/tests/browser/browser_usercharacteristics_gamepads.js --bugs`
- Expected: a Bugs section, even if it only says none were found.
- Got: the same output as without `--bugs`, with no line about bugs, so I could not tell "no bug" from "the lookup did not run". I had to search Bugzilla's REST API by hand.

## `fx-tests errors --message` does not search the harness's process-output lines

- Command: `fx-tests errors --message "Glean should not be initialized multiple times"`
- Expected: the tests and jobs whose logs contain this Rust `[ERROR glean_core]` line. It shows up as an `output` marker in the resource-usage profiles of failing jobs (e.g. B4bdVfUSSSy9A0fdw7zziA).
- Got: "No markers matched" among 54M markers. The errors file seems to keep only C++ warnings, JS errors and console.* messages, not raw process stdout.
- Workaround: loaded each job's resource-usage profile in profiler-cli and searched it for `glean_core`.
