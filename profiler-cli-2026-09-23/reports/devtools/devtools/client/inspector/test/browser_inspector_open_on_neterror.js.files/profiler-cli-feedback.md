## `zoom push` with the time format the tool itself prints silently zooms elsewhere

- Command: `profiler-cli zoom push 11m43s,14m12s --session browser_inspector_open_on_neterror.js-1`
- Expected: a zoom to 703s..852s (the tool prints marker times as `t=11m44s`), or an error saying the format is not accepted.
- Got: exit 0, `View: ts-1→ts-2 (3s)` at the start of the profile; the next `thread markers --list` showed one unrelated marker, which looked like "nothing happened during the test".
- Workaround: `zoom push 703,852` (seconds).

## Question: "how busy was the machine during this window?" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` piped into a Python script averaging `data.cpuPercent` over a `zoom push` range.
- The default output of `thread markers --search "name:CPU Use"` gives only count and duration stats of the markers. It could show min/avg/max of the CPU Percent field over the markers in view, which is the whole question for a timeout.
