## Question: "how many tests passed / timed out after time T, in a resource-usage profile"
- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0`
- Expected: a way to restrict to markers starting after a time and group by status (e.g. `zoom push 131.676,end` plus `--group-by field:status`).
- Got: I had to use `--json` and a Python script to split `test` markers by start time and by the status prefix of their label, and to rank one test among them.
- Workaround: `--json` plus a script.

## Question: "the exact time of a marker"
- Command: `profiler-cli thread markers --search <test> --list` and `marker info m-1`
- Expected: millisecond times (e.g. t=133.344s), because the gaps that matter here are 36 ms.
- Got: `t=2m13s` in both the list and `marker info`, so the precise start only came from `--json`.
- Workaround: `--json`.
