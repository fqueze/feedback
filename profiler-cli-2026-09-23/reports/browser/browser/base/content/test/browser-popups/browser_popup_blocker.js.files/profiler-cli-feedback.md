## Marker times over one minute lose sub-second precision in text output

- Command: `profiler-cli thread markers --category Test --search browser_popup_blocker.js --list --limit 0 --session <s>` (and `marker info m-45 m-53 ...`)
- Expected: timestamps precise enough to order events a few hundred ms apart (e.g. `t=69.850s` or `t=1m9.850s`).
- Got: `t=1m10s` for every marker past 60 s, in both `--list` and `marker info`. Test steps 0.1-0.9 s apart become indistinguishable.
- Workaround: `--json` and a Python one-liner printing `start/1000`.
- Question this answered: "at what exact time did each test step happen?"

## Resource-usage profile: machine CPU only as markers, `counter list` says none

- Command: `profiler-cli load <.../profile_resource-usage.json> --session s; profiler-cli profile info; profiler-cli counter list`
- Expected: a way to read the machine's CPU/memory/IO over a time range ("was the machine saturated between t=104 s and t=122 s?").
- Got: `profile info` says "CPU activity over time: No significant activity", `counter list` says "No counters in this profile". The data is there as 13,000 `CPU Use` / `Memory` / `IO` markers, one per 100 ms.
- Workaround: `thread markers --search "name:CPU Use,name:Memory,name:IO" --list --limit 0 --json` and a Python script bucketing `cpuPercent`, `system_pct`, `used`, `read_bytes` per second.
- Question: "how busy was the machine, per second, over this range?" A summary of those markers per bucket (like `counter info`'s "over time") would answer it.

