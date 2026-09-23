## Log markers print "(empty)" for Level and Message although the profile has them

- Command: `profiler-cli thread markers --thread t-18 --category Test --list --limit 0 --session <s>` and `profiler-cli marker info m-1551 --session <s>` on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/BBz3g11qS0G3zFCmicKzGQ/runs/0/artifacts/public/test_info/profile_test_copypaste.html.json
- Expected: the mochitest `INFO` lines, e.g. `Initializing clipboard with "waitForClipboard-known-value-…"...`
- Got: every `INFO` marker (type `Log`) listed as `[(empty)] INFO: (empty)`, and `marker info` shows `Level: (empty)`, `Message: (empty)`. `marker info --json` has the text in `fields[].value` but `formattedValue: "(empty)"`, so the formatter drops string values of this schema.
- Cost: I first concluded the per-test profile did not record the INFO text, and had to align the test steps on TEST-PASS markers only.
- Workaround: `marker info <m> --json` and read `fields[].value`.

## Question: machine CPU % over one test's time range, from a resource-usage profile

- Command: `profiler-cli zoom push m-1` (the `test` marker) then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- The list shows only start times rounded to the second (`t=6m7s`) and the duration, not the CPU percent, so I needed `--json` and a script to get "CPU % per 100 ms sample during this test".
- What would have answered it: the `CPU Percent` field in the list row (or a `counter`-like summary of CPU Use markers over the zoomed range), and sub-second timestamps when the profile is minutes long.
