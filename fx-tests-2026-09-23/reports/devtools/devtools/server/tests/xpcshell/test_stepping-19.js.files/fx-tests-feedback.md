## Question: the resource-usage profile of a passing job on a config that never fails, to compare machine load

- Command: `fx-tests test devtools/server/tests/xpcshell/test_stepping-19.js --profiles --config test-macosx1500-aarch64-vms/debug-xpcshell`
- Expected: a few resource-usage profile URLs for jobs of that config (545 passing runs), since the section is headed "Profiles (raw artifact URLs, for profiler-cli)" and the guide says `--profiles` "separates the test is slow from the machine was saturated".
- Got: the header and a note pointing at per-test profiles, but no URL at all.
- Workaround: none; skipped the comparison (whether macOS 15 aarch64 debug workers are as CPU-saturated as the failing 10.15 ones).
- What could have shown it: a handful of task IDs / resource-usage URLs for passing runs on the filtered config.
