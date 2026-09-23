## Marker list timestamps lose sub-second precision past one minute

- Question: in what order, to the millisecond, did the test's log markers and the DOM events (command, TabOpen, TabSelect) happen?
- Command: `profiler-cli thread markers --category Test --search browser_accessibility_print_to_json --list --limit 0 --session <s>`
- Expected: times like `t=63.936s` (or `1m3.936s`).
- Got: every row at `t=1m4s` / `t=1m9s`; 10 markers spanning 600 ms print the same time, so the order and gaps cannot be read.
- Workaround: re-ran with `--json` and printed `.flatMarkers[].start` with a script.

## `thread list` CPU column ignores the zoom

- Question: which threads were busy during the 5 s wait (zoomed with `zoom push 64.2,69.18`)?
- Command: `profiler-cli thread list --session <s>` after `zoom push`.
- Expected: CPU per thread within the committed range (the header says `View: ts-X→ts-Y (4.980s)`).
- Got: full-profile CPU totals, same numbers as unzoomed; `profile info --all` likewise.
- Workaround: looped `thread select` + `thread samples --include-idle` over 7 threads.

## Machine CPU / memory / disk over a range in a resource-usage profile

- Question: was the machine saturated (CPU, system CPU, memory, disk reads) between 101 s and 117 s?
- Command: `profiler-cli thread markers --search "name:CPU Use,name:Memory,name:IO" --list --limit 0 --json --session <s>` piped into a Python script bucketing per second.
- Expected: something like `profile info`'s "CPU activity over time", but from these markers: per-second (or per-bucket) avg/max CPU %, system %, memory used, bytes read, for the zoomed range.
- Got: `profile info` says "No significant activity" and `counter list` "No counters"; the data is only in ~480 individual markers.
- Workaround: the script.
