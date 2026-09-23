## When exactly did this marker start (past the first minute)

- Command: `profiler-cli thread markers --session <s> --search test_framebindings-04 --list --limit 0` (and `marker info m-1`) on a resource-usage profile of a 3.5-minute xpcshell job.
- Question: at what millisecond did the test start, and how long after it did its "will retry" line come (these are 30-40 ms apart)?
- Expected: millisecond times, as the list prints for t < 60 s (`t=46.514s`).
- Got: past one minute, times are rounded to the second (`t=2m13s` for both the test start and its "will retry"), in both `--list` and `marker info`, so the 40 ms gap and the ordering against a `Failed to launch` warning at `t=2m12s` are unreadable.
- Workaround: `--list --json` piped through python to print `start`.
- Could have shown: `t=2m13.166s` (keep ms precision in m/s format).

## Which test's process logged this warning

- Command: `profiler-cli thread markers --session <s> --search "Failed to launch" --list --limit 0` on a resource-usage profile.
- Question: how many of the 25 `Failed to launch … (Error:-2147009284)` warnings came from each test's xpcshell, and when did the one from `test_ext_background_early_shutdown.js` occur?
- Expected: the `Test` field (present in `marker info`) in the list row, or a `--group-by field:test` that works on it.
- Got: rows show message and file only; `marker info` shows `Test:` but one marker at a time.
- Workaround: `--list --json` piped through python, counting `fields[key=test]`.
