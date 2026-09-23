## Question: which tests are behind one crash signature?
- Command: `fx-tests failures --message "child process hang at shutdown" --tests`
- Expected: the tests whose failures carry this crash signature (fx-tests crashes lists it with 19,349 crashes in 503 tests).
- Got: "No failure matched". `fx-tests crashes --signature ...` gives only counts, with no test list and no --tests option.
- Workaround: `fx-tests issues --path <dir> --group-by test --type crash`, which counts every crash signature, not just this one.

## Question: for the tests running at the same time as mine, how long did each take from start to shutdown, and did it pass or crash?
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json` plus `--search "may be hanging at shutdown" --json`, joined with awk.
- The default output could show: for the `test` markers overlapping a given time, their status and duration. For a CRASH, the `test` marker's end includes the minidump processing (minutes), so the real shutdown time has to come from another marker.
