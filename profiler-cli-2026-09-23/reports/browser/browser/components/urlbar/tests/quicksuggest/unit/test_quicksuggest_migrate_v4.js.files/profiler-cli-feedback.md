## Question: "was the machine saturated while this test ran?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging `cpuPercent` weighted by duration per minute and per 2 s around the test.
- The resource-usage profile carries whole-machine CPU as thousands of `CPU Use` interval markers, and no counter (`counter list` says none). Neither `profile info`'s "CPU activity over time" ("No significant activity") nor `thread markers` summarises their values, so the one number that separates "slow machine" from "slow test" needs a script.
- What the output could have shown: `profile info` (or `counter list`) exposing the `CPU Use` markers as a machine CPU track with per-bucket averages, respecting `zoom push`, e.g. "machine CPU in 4m42s–5m08s: 100% (12 cores)".
