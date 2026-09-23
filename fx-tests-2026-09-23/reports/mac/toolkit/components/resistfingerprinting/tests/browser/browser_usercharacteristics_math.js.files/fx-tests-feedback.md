## Question: the tasks where this test passed, on a given config

- Command: `fx-tests test toolkit/components/resistfingerprinting/tests/browser/browser_usercharacteristics_math.js --task-ids --limit 0`
- Needed: a few passing jobs of the same config that ran the same manifest, to check whether a log line (`[ERROR glean_core] Glean should not be initialized multiple times`) seen in every failing job is absent from passing ones.
- Got: only failing tasks. `--coverage` gives configs, not tasks.
- Workaround: Treeherder `api/jobs/?push_id=` per push, then a script downloading each chunk's `profile_resource-usage.json` to find which chunk ran the manifest and grep it. About 80 downloads for 6 passing sessions.
- What the output could show: `--task-ids --passing` (or `--task-ids --all`) listing passing task IDs per config, ideally with the chunk that ran the test.

## Question: which jobs logged this harness `output` line, and in which test?

- Command: `fx-tests errors --message 'Glean should not be initialized multiple times'`
- Expected: the jobs/tests printing that stderr line.
- Got: `No markers matched` (the errors file covers C++ warnings, JS errors, console.* — not plain `output` lines from Rust `log::error!`).
- Workaround: grep each job's resource-usage profile JSON for `"test":"...","message":"[ERROR glean_core] ..."`.
- What the output could show: include `output` lines matching `[ERROR ` / `[WARN ` prefixes (Rust log output) in the errors data.
