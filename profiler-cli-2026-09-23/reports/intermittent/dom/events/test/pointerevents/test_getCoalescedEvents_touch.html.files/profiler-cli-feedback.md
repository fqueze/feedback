## TEST-* markers of a popup-window plain mochitest are not on the parent main thread

- Command: `profiler-cli thread markers --category Test --search <test file> --list --limit 0` on t-0 (parent GeckoMain), as the brief suggests.
- Expected: the test's TEST-PASS / TEST-UNEXPECTED-FAIL log.
- Got: 15 SpecialPowers markers only. For mochitest-plain, the TestStatus markers are recorded in the content process running the test (t-12 here), including the ones logged via `opener`.
- Workaround: `profiler-cli profile markers --search <test file>` sweeps all threads and found them. A hint in `thread markers` when a --search matches nothing on the selected thread but matches on others would save the step.
