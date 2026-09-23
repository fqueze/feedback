## Question: "was the machine I/O-bound during each of these 9 time windows?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (after `zoom push 190.5,193.6`)
- Expected: the iowait / CPU % of each `CPU Use` row, or min/mean/max per field over the zoomed range, so that one command per window answers it.
- Got: rows with name, time and duration only. The iowait value is only in `marker info` or `--json` `fields`. Also, `marker info` prints `iowait: 0.875` while the `--json` field `iowait_pct` of the same row reads `88.3%`/`87.5%`-style formatted strings, so the two views are not obviously the same number.
- Workaround: `--json` over the whole profile, and a script (`iowait.py` here) mapping each window from the job log onto profile time and averaging `iowait_pct`.
