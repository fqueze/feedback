## Question: what was the machine's CPU use while test X ran? (resource-usage profile)

- Command: `profiler-cli profile info --session test_dictionary_replacement.js-4` and `profiler-cli counter list` on
  https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/VUMDPCekRmqTE5dT5Y3UXA/runs/0/artifacts/public/test_info/profile_resource-usage.json
- Expected: a CPU track or summary for a time range (e.g. after `zoom push 271.5,272.6`, "machine CPU 0-29%, avg 13%").
- Got: `CPU activity over time: No significant activity.` and `No counters in this profile.` The data exists only as
  `CPU Use` markers (one per ~100 ms), so I had to list them and run `marker info` on 12 handles to read `CPU Percent`.
- Workaround: `thread markers --search "name:CPU Use" --list` then `marker info m-148 m-123 ... | rg "CPU Percent"`.
- What would have answered it: `profile info` (or `thread markers --group-by name`) on a resource-usage profile summarising
  the `CPU Use` markers in the current zoom (min/avg/max CPU Percent), instead of reporting "No significant activity".
