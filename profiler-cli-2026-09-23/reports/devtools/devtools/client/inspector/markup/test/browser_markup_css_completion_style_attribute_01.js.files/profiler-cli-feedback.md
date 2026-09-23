# profiler-cli feedback

## Question: "how busy was the machine between t=198 s and t=226 s?" (resource-usage profile)

- Command: `profiler-cli counter list --session <id>` on
  `.../RpYXmdrTRSmCYfSIpLaQPw/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: a CPU counter, or some summary of machine CPU use per time bucket, as the brief
  says the resource-usage profile has "the CPU use of the whole machine".
- Got: `No counters in this profile.` The CPU data is only in `CPU Use` interval markers, and
  `thread markers --search "name:CPU Use" --list` prints each marker without its
  `CPU Percent` field.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python
  script averaging `data.cpuPercent` per second.
- What would have answered it: `counter list`/`counter info` exposing the resource-usage CPU
  markers as a counter, or `--list` showing the `CPU Percent` field of `CPU` markers inline.
