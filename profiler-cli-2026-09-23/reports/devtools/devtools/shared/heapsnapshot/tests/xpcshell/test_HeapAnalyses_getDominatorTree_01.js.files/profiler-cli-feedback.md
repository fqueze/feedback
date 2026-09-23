# profiler-cli feedback (test_HeapAnalyses_getDominatorTree_01.js)

## Question: "which processes changed name between consecutive ps listings" (Android xpcshell resource-usage profile)

- Command: `profiler-cli thread markers --session S --search get_process_list --list --limit 0 --json` (53 MB), then a Python script parsing each `get_process_list: [[pid, 'name', 'user'], ...]` label.
- Expected: some way to see a Text/Log marker's value diffed against the previous marker of the same name, or at least to search within the payload and print only the matching fragment.
- Got: each listing is one huge label (~40 KB, every process on the device); the text output truncates it, and `--search xpcshell2` matches every listing because the full list is in each one.
- Workaround: dump to JSON and parse. What would have answered it: a `--grep`-style option that prints only the matched fragment of a long label (e.g. `[\d+, '...:xpcshell\d']` entries), so one could eyeball which pid carried which name at which time.

## Question: "which slot (`XpcshellTestRunnerService$iN`) ran which test"

- Command: `thread markers --search launch_application --list` — each label is ~3.5 KB; the slot is near the start and `_TEST_NAME` near the end, so the truncated text output shows the slot but never the test name.
- Workaround: `--json` and a regex over the label. A way to show the middle/end of a long label (or the fragment around the search hit) would have avoided the script.
