## `test --bugs` prints no bugs

- Command: `fx-tests test dom/webtransport/test/xpcshell/test_close.js --bugs`
- Expected: the sheriff-annotated bugs naming the test (2070388 at least; 55 annotated jobs per `fx-tests intermittent --bug 2070388 --since 21 --tree all`).
- Got: exactly the output without `--bugs`, exit 0, nothing on stderr: no bug section and no "none found" line.
- Workaround: the bug number was given to me.

## `test --task-ids --issue <n>` prints the task list twice

- Command: `fx-tests test dom/webtransport/test/xpcshell/test_close.js --task-ids --limit 0 --issue 4`
- Expected: each failing job once.
- Got: the 2026-09-08 .. 2026-09-15 list twice (same jobs, slightly different order within a day), then a third block for the `-cf` job. About 150 lines where 70 would do.
- Workaround: none needed, read the first block.

## `intermittent --bug` default window hid a bug whose annotations stopped a week ago

- Command: `fx-tests intermittent --bug 2070388`
- Expected: the bug's annotations, or a hint that older ones exist.
- Got: "no sheriff annotations ... between 2026-09-16 and 2026-09-22" (true: the test was skipped on 2026-09-15). The hint to widen was there; `--since 21 --tree all` found 63.
- Workaround: `--since 21 --tree all`. Saying how many annotations exist outside the window would save the second call.
