# profiler-cli feedback (browser_storage_cookies_delete_all.js)

## Question: was the machine busy when the timers fired late?

- Command: `profiler-cli load <.../UJYKq40TR8-9bVYal61Yig/runs/0/artifacts/public/test_info/profile_resource-usage.json> --session bscda-2`, then `profile info` and `counter list`
- Expected: whole-machine CPU use over the job, which the brief says the resource-usage profile carries.
- Got: `profile info` shows 1 thread (`mach`, 0.000ms) and "No significant activity". `counter list` reports "No counters in this profile". The `test` markers and TEST-* markers are there.
- Workaround: none. The report says the machine's load is unknown.
