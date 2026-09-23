## Question: in which order, to the millisecond, did the test's log lines happen?

- Command: `profiler-cli thread markers --session <s> --search test_browserGlue_bookmarkshtml --list --limit 0`
- Expected: timestamps precise enough to order log lines that are 1-100 ms apart (here: `exiting test`, the HTML parser warning, the `profile-before-change` check, the aborted transaction, and the storage crash all fall within 250 ms).
- Got: every row printed as `t=3m44s`, so the 30 lines of interest were indistinguishable in time.
- Workaround: `--json` and printing `start` for each `flatMarkers` entry. Printing ms precision in `--list` (e.g. `t=223.907s`, or `3m43.907s`) whenever rows share the same rounded time would answer it.
