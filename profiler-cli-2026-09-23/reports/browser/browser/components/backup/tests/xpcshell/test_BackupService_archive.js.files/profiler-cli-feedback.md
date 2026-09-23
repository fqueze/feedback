## Question: "how long between `exiting test` and the crash message" (times past 60 s)

- Command: `profiler-cli thread markers --session <s> --search test_BackupService_archive --list --limit 0` and `profiler-cli marker info m-235 m-237 m-239 m-240 --session <s>` on a resource-usage profile (task WL7ALnBBQEm7QWBj8Dq4qg).
- Expected: sub-second timestamps, as for markers before t=60s (`t=43.829s`).
- Got: every marker in that range printed as `t=1m16s` / `Time: 1m16s (instant)`, so the 250 ms gap between `exiting test` and `Storage connection not closed` was invisible.
- Workaround: `marker info ... --json` and read `start` (76110.581 vs 76361.581 ms).
- Could show: keep milliseconds for minute-range times (`1m16.111s`).
