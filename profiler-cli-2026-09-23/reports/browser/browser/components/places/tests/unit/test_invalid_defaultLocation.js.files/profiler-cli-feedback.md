## Question: "what was the machine's CPU use over this range" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "CPU Use" --list --limit 0` after `zoom push 38,60`, on the resource-usage profile of ZgUlY0ZlRvmHZNTOfia3AA.
- Expected: one row per `CPU Use` marker with its CPU percentage, so a saturation or an idle stretch can be read off the list.
- Got: rows with name, time and duration only (`m-441  CPU Use  t=53.266s  109ms`), no value. The percentage is only in `marker info` ("CPU Percent: 0.0%"), one handle at a time.
- Workaround: `marker info` on a handful of hand-picked handles.
- What would have answered it: the `CPU Percent` field in the list row for `CPU` markers (as `Test` markers show their status), or a `counter`-style over-time summary of CPU Use for the zoomed range.
