## Machine CPU use over a window

- Question: "what was the machine's CPU use between 20m15s and 20m20s" (to rule out a saturated machine).
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0 --json` inside a `zoom push 1215,1220`, then a Python script averaging `fields[cpuPercent]`.
- Expected: a summary (mean/max of `cpuPercent`) for the zoomed range, like `counter info` gives for counters.
- Got: 51 instant-looking rows without the value in the text output (`CPU Use  t=20m15s  101ms`), so the default output could not answer; needed `--json` plus a script.
- Could have shown: the `cpuPercent` field in the list row, or mean/max per numeric field in the aggregated `thread markers --search "name:CPU Use"` view.

## Sandbox session dir

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session <s>`
- Expected: load. Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, and then a second, misleading error `Unknown session ...: no metadata found`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. The error message did suggest this, which helped.
