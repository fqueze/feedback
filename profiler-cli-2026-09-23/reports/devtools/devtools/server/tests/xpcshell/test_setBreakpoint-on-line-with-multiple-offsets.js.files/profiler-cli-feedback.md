## Question: "which tests started after time T, counted by status, and which were running at T" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --json --session <s>`, then a Python script splitting `label` on ` — ` and comparing `start` against the first `Failed to launch` marker's start.
- Expected: a way to get this from default output, e.g. `thread markers --search name:test --group-by field:status` combined with `zoom push T,end` counting markers that *start* in the range (today a zoom includes markers overlapping the range, so the long `parallel`/TIMEOUT markers that started before T are counted too).
- Got: no start-in-range option and no status grouping on `test` markers from the text output.
- Workaround: the JSON + script above.

## Question: "exact time of this instant marker"

- Command: `profiler-cli marker info m-6 --session <s>` on a 2-minute profile.
- Expected: millisecond time (e.g. `92.975s`).
- Got: `Time: 1m33s (instant)`, the same for dozens of markers in the same second, and `thread markers --list` also prints `t=1m33s`. Ordering markers 35 ms apart needed `--json`.
- Workaround: `marker info ... --json` and read `start`.
