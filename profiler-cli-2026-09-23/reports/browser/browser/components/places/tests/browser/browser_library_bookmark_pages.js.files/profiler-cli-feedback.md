## Sub-second ordering of markers past the first minute
- Command: `profiler-cli thread markers --session S --search "places.sqlite,sqlite_store" --list --limit 0`
- Question: in which order did a Counter marker (SQLITE_BUSY), a DeferredTask interval end and an async-error Runnable happen, all within the same second at t=2m19s-2m20s?
- Got: times printed as `t=2m19s` / `t=2m20s` and durations only, so the order within a second is unreadable; `marker info` also prints `Time: 2m19s - 2m20s`.
- Workaround: `--json` and read `start`/`duration` in ms. The text output could print ms (e.g. `t=2m19.577s`) when the list spans less than a few seconds, or always.

## Resource-usage profile: CPU looks absent
- Command: `profiler-cli profile info --session <resource-usage>` and `profiler-cli counter list`
- Expected: the machine CPU track.
- Got: "CPU activity over time: No significant activity." and "No counters in this profile." The CPU is in `CPU Use` markers (cpuPercent/idle_pct), found only via `thread markers` by-name overview.
- Suggestion: have `profile info` mention the `CPU Use` markers for such profiles.

## Correlating two marker sets (question: "is every X inside a Y interval, and how many X before/after time T?")
- Command: `thread markers --search notifyErrorOnCallingThread --list --json` + `thread markers --search "places.sqlite#1: waiting for clients" --list --json`, then a Python script.
- Question: were there any async statement errors before the SQLITE_BUSY, and does each later Sqlite.sys.mjs Transaction contain one?
- What could show it: a `--during-marker <search>` / `--outside-marker` filter for `thread markers` (it exists for samples), plus a before/after count split at a timestamp.
