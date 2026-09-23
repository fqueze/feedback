## In which order did markers within the same second happen?

- Command: `profiler-cli thread markers --session <s> --search browser_treeWidget_mouse_interaction --list --limit 0` on a 34-minute resource-usage profile.
- Expected: timestamps precise enough to order the test's PASS/FAIL markers, which are milliseconds apart.
- Got: every marker of the test shows `t=25m11s`; the list order alone cannot be trusted to tell whether a FAIL was logged before or after a PASS.
- Workaround: `--json` and a Python script printing `flatMarkers[].start`.
- What the output could have shown: millisecond precision (e.g. `25m11.696s`) in `--list` mode, at least when the listed markers span a few seconds.
