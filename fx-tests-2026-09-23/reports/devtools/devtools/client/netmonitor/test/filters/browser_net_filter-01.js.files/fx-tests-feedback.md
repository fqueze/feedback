## The task IDs of the try jobs that ran one test

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/netmonitor/test/filters/browser_net_filter-01.js --all-jobs --task-ids`
- Expected: a task ID per job in the per-configuration ran/passed table, to pick a passing job's resource-usage profile.
- Got: the same table as without `--task-ids`; the flag is silently ignored with `--test`.
- Workaround: looked the job names up in a Treeherder jobs dump (`todo.files/try-jobs.json`) with a script.
- What the output could show: a task ID column (or one line per job) when `--task-ids` is given.

## Was the config where the test is skipped scheduled on the try push at all?

- Command: same as above.
- Expected: for a test skipped on central on one config (here `test-linux2404-64/debug-mochitest-devtools-chrome-http3`), a line saying whether that config was on the push, and whether the test was skipped there.
- Got: only the configs that ran the test; a config absent from the push and a config where the manifest still skips the test both read as absence.
- Workaround: grepped the push's job list for `-http3` with a script, and read the manifest at the pushed revision.
- What the output could show: "Not on this push: <configs where central runs or skips it>", and "Skipped here: <config>: <condition>".
