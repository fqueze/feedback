## `zoom push` silently accepts the tool's own "1m30s" time format as a 1 ns range
- Command: `profiler-cli zoom push 1m30s,1m40s --session <s>`
- Expected: either a 90–100 s zoom (marker lists print times as `t=1m38s`, so that is the format one copies), or an error.
- Got: `View: ts-1→ts-2 (1.000ns)` and no warning; the next marker query returned 1 unrelated marker. (A range beyond the profile does warn.)
- Workaround: pass seconds, `zoom push 90,100`.

## A bare negative term in `thread markers --search` silently matches nothing
- Command: `profiler-cli thread markers --search "-name:CPU Use,-name:Memory,-Full log" --list`
- Expected: `-Full log` to exclude markers whose text contains "Full log", or an error saying negatives need `field:`.
- Got: `0 markers`, "No markers match the specified filters." — looks like an empty time range.
- Workaround: drop the bare negative and filter the output with grep.
