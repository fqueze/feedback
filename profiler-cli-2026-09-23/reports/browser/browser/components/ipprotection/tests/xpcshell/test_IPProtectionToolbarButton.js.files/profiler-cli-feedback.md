## Question: "at what exact time did these markers happen?" (browser-test_IPProtectionToolbarButton.js)

- Command: `profiler-cli thread markers --search test_IPProtectionToolbarButton --list --limit 0 --session <s>`
- Expected: sub-second start times, to order "exiting test", "may be hanging ... 8000ms", "Reply_Init too late", "attempting crash report".
- Got: `t=10m7s`, `t=10m10s` ... rounded to the second 10 minutes into the profile, so several steps share one timestamp.
- Workaround: `marker info m-a m-b ... --json` and reading `start`. Its top-level shape differed between two calls (a list of records vs. a dict), which broke the one-liner the second time.

## Question: "what was the whole machine's CPU use over this window?" (browser-test_IPProtectionToolbarButton.js)

- Command: `profiler-cli counter list` on a resource-usage profile says `No counters in this profile`; `profile info` says `No significant activity`.
- The data is there as `CPU Use` interval markers with a `CPU Percent` field, but the list does not print the field.
- Workaround: `thread markers --search "name:CPU Use" --list --json` plus a script to bucket `cpuPercent` per second. A per-bucket summary of those markers after a `zoom push` (like `counter info`'s "over time") would answer it.
